# JUnit and Mockito Interview Questions - Continued

## **PART 4: Mockito Advanced Concepts**

### **51. What is the difference between verify() and when()?**

**when()** - Sets up behavior (stubbing) - what mock should return
**verify()** - Checks behavior (verification) - was method actually called

**Example:**
```java
// Stubbing - defining what should happen
when(repository.findById(1)).thenReturn(employee);

// Verification - checking what actually happened
verify(repository).findById(1);
```

Think of it as: when() is planning, verify() is confirming.

---

### **52. What happens if you call a method on a mock without stubbing it?**

The mock returns default values:
- Objects return null
- Primitives return 0, false, etc.
- Collections return empty collections

**Example:**
```java
@Mock
private EmployeeRepository repository;

Employee emp = repository.findById(1); // Returns null (not stubbed)
int count = repository.count(); // Returns 0 (not stubbed)
```

---

### **53. How do you stub multiple return values?**

Use multiple thenReturn() calls or pass multiple values to one thenReturn().

**Example:**
```java
// First call returns 10, second returns 20, third returns 30
when(service.getCount())
    .thenReturn(10)
    .thenReturn(20)
    .thenReturn(30);

// Or shorter:
when(service.getCount()).thenReturn(10, 20, 30);

int first = service.getCount();  // 10
int second = service.getCount(); // 20
int third = service.getCount();  // 30
```

---

### **54. What is thenAnswer() in Mockito?**

thenAnswer() lets you write custom logic to generate return values dynamically based on method arguments.

**Example:**
```java
when(calculator.add(anyInt(), anyInt())).thenAnswer(invocation -> {
    int a = invocation.getArgument(0);
    int b = invocation.getArgument(1);
    return a + b;
});

int result = calculator.add(5, 3); // Returns 8
```

Useful when return value depends on input parameters.

---

### **55. What is thenCallRealMethod() in Mockito?**

It calls the actual real method on a mock object instead of returning a default value.

**Example:**
```java
@Mock
private ReportService service;

when(service.calculateScore(anyInt(), anyInt())).thenCallRealMethod();

// Now it executes actual calculateScore() logic
double score = service.calculateScore(80, 90);
```

Mainly used with partial mocking or spies.

---

### **56. What is the difference between any() and anyObject()?**

They're almost the same - both match any object. In modern Mockito, use `any()`. `anyObject()` is deprecated.

**Example:**
```java
// Modern way
when(service.process(any())).thenReturn(result);

// Old way (deprecated)
when(service.process(anyObject())).thenReturn(result);
```

---

### **57. Can you mix argument matchers with real values?**

No! If you use matchers, all arguments must be matchers. Use `eq()` for exact values when mixing.

**Example:**
```java
// WRONG - mixing matcher and real value
when(service.calculate(anyInt(), 100)).thenReturn(result);

// CORRECT - all matchers
when(service.calculate(anyInt(), eq(100))).thenReturn(result);
```

---

### **58. What is verifyNoMoreInteractions()?**

It verifies that no other methods were called on the mock beyond what you already verified.

**Example:**
```java
@Test
public void testDeleteOnly() {
    reportService.deleteReport(1);
    
    verify(repository).deleteById(1);
    verifyNoMoreInteractions(repository); // Fails if any other method was called
}
```

Useful for strict testing, but can make tests brittle.

---

### **59. What is verifyZeroInteractions()?**

Verifies that a mock was never used at all - no methods were called on it.

**Example:**
```java
@Test
public void testNoEmailSent() {
    reportService.generateReport(1);
    
    verifyZeroInteractions(emailService); // Email service should not be touched
}
```

---

### **60. What is InOrder verification?**

InOrder verifies that methods were called in a specific sequence.

**Example:**
```java
@Test
public void testOrderOfOperations() {
    reportService.processReport(1);
    
    InOrder inOrder = inOrder(repository, emailService);
    inOrder.verify(repository).findById(1);
    inOrder.verify(repository).save(any());
    inOrder.verify(emailService).send(anyString());
}
```

Ensures save happens before email is sent.

---

### **61. What is reset() in Mockito and should you use it?**

`reset()` clears all stubbing and interactions on a mock. Generally avoid it - if you need reset(), you probably need a new test.

**Example:**
```java
when(repository.count()).thenReturn(10);
// ... test code ...
reset(repository); // Clears the stubbing
when(repository.count()).thenReturn(20); // New stubbing
```

Better practice: Create fresh mocks for each test.

---

### **62. What is @Captor annotation?**

@Captor is a shorthand for creating ArgumentCaptor objects.

**Example:**
```java
@Captor
private ArgumentCaptor<Employee> employeeCaptor;

@Test
public void test() {
    service.save(employee);
    verify(repository).save(employeeCaptor.capture());
    assertEquals("John", employeeCaptor.getValue().getName());
}
```

---

### **63. How do you capture multiple arguments?**

Use `getAllValues()` to get all captured values from multiple calls.

**Example:**
```java
@Captor
private ArgumentCaptor<String> emailCaptor;

service.sendBulkEmails(employees); // Sends 3 emails

verify(emailService, times(3)).send(emailCaptor.capture());
List<String> allEmails = emailCaptor.getAllValues();
assertEquals(3, allEmails.size());
```

---

### **64. What is lenient() stubbing?**

Lenient stubbing doesn't fail the test if the stubbing isn't used. Useful for setup methods with multiple stubs where not all tests use all stubs.

**Example:**
```java
@Before
public void setup() {
    lenient().when(repository.count()).thenReturn(10);
    lenient().when(repository.findAll()).thenReturn(employees);
    // Some tests only use count(), some only use findAll()
}
```

---

### **65. What is deep stubbing?**

Deep stubbing allows you to stub chained method calls.

**Example:**
```java
@Mock(answer = Answers.RETURNS_DEEP_STUBS)
private Employee employee;

when(employee.getDepartment().getManager().getName()).thenReturn("John");

// Without deep stubs, you'd need:
// when(employee.getDepartment()).thenReturn(dept);
// when(dept.getManager()).thenReturn(manager);
// when(manager.getName()).thenReturn("John");
```

Use sparingly - often indicates poor design.

---

### **66. How do you test methods that have void return type?**

Test their side effects - verify they call other methods or change object state.

**Example:**
```java
@Test
public void testUpdateEmployee() {
    Employee emp = new Employee("John", 50000);
    service.updateSalary(emp, 60000); // void method
    
    // Verify side effect
    assertEquals(60000, emp.getSalary());
    verify(repository).save(emp);
}
```

---

### **67. What is MockSettings?**

MockSettings provides advanced configuration when creating mocks.

**Example:**
```java
EmployeeRepository repository = mock(EmployeeRepository.class, 
    withSettings()
        .name("EmployeeRepo")
        .verboseLogging()
        .serializable()
);
```

Rarely needed in normal testing.

---

### **68. Can you mock final classes in Mockito?**

In older Mockito (before 2.0), no. In Mockito 2.0+, yes, with inline mock maker enabled.

**Example:**
```java
// Create file: src/test/resources/mockito-extensions/org.mockito.plugins.MockMaker
// Content: mock-maker-inline

@Mock
private FinalClass finalClass; // Now works
```

---

### **69. What is the difference between @Mock and mock()?**

**@Mock** - Annotation-based, cleaner code, requires initialization
**mock()** - Method-based, works immediately, more verbose

**Example:**
```java
// Using annotation
@Mock
private ReportRepository repository;

// Using method
private ReportRepository repository = mock(ReportRepository.class);
```

Both do the same thing, annotations are preferred for cleaner code.

---

### **70. How do you mock constructors?**

Mockito doesn't mock constructors directly. Use dependency injection or PowerMock (different framework).

**Example - Better approach:**
```java
// Instead of: new Employee() inside method
// Use: dependency injection
public class ReportService {
    private EmployeeFactory factory; // Inject this
    
    public void createEmployee() {
        Employee emp = factory.create(); // Now you can mock factory
    }
}
```

---

## **PART 5: Common Testing Scenarios**

### **71. How do you test a method that calls another method in the same class?**

If it's a public method, test it directly. If it's private, test through the public method that calls it.

**Example:**
```java
public class ReportService {
    public double calculateFinalScore(int score) {
        return adjustScore(score); // private method
    }
    
    private double adjustScore(int score) {
        return score * 1.1;
    }
}

@Test
public void test() {
    // Test calculateFinalScore, which internally tests adjustScore
    assertEquals(110, service.calculateFinalScore(100), 0.01);
}
```

---

### **72. How do you test methods that depend on current date/time?**

Extract date/time creation into a separate method or class that you can mock.

**Example:**
```java
public class ReportService {
    private DateProvider dateProvider;
    
    public boolean isReportExpired(Report report) {
        Date now = dateProvider.getCurrentDate(); // Mockable
        return report.getDate().before(now);
    }
}

@Test
public void testExpired() {
    Date testDate = new Date(2024, 1, 1);
    when(dateProvider.getCurrentDate()).thenReturn(testDate);
    // Now you control "current" date
}
```

---

### **73. How do you test methods that have random behavior?**

Mock the random number generator or extract randomness into a mockable component.

**Example:**
```java
public class ReportService {
    private RandomGenerator random;
    
    public Employee selectRandomEmployee(List<Employee> employees) {
        int index = random.nextInt(employees.size());
        return employees.get(index);
    }
}

@Test
public void test() {
    when(random.nextInt(3)).thenReturn(1); // Always returns index 1
    Employee result = service.selectRandomEmployee(employees);
    assertEquals(employees.get(1), result);
}
```

---

### **74. How do you test exception handling?**

Use expected exceptions or try-catch blocks to verify exceptions are handled correctly.

**Example:**
```java
@Test
public void testExceptionHandling() {
    when(repository.findById(1)).thenThrow(new DataAccessException());
    
    String result = service.getEmployeeName(1);
    
    assertEquals("Unknown", result); // Service handles exception gracefully
    verify(logger).error(contains("Employee not found"));
}
```

---

### **75. How do you test asynchronous methods?**

Use CountDownLatch, CompletableFuture, or Awaitility library to wait for async completion.

**Example:**
```java
@Test
public void testAsyncMethod() throws Exception {
    CountDownLatch latch = new CountDownLatch(1);
    
    service.processAsync(data, result -> {
        assertEquals(expected, result);
        latch.countDown();
    });
    
    latch.await(5, TimeUnit.SECONDS); // Wait max 5 seconds
}
```

---

### **76. How do you test methods that send emails?**

Mock the email service and verify it was called with correct parameters.

**Example:**
```java
@Mock
private EmailService emailService;

@Test
public void testEmailSent() {
    service.notifyManager(employee);
    
    verify(emailService).send(
        eq("manager@company.com"),
        contains("Performance Review"),
        anyString()
    );
}
```

---

### **77. How do you test file operations?**

Use temporary files, mock file system, or test in-memory file systems.

**Example:**
```java
@Test
public void testFileWrite() throws IOException {
    File tempFile = File.createTempFile("test", ".txt");
    tempFile.deleteOnExit();
    
    service.writeReport(tempFile, data);
    
    String content = new String(Files.readAllBytes(tempFile.toPath()));
    assertTrue(content.contains("Expected data"));
}
```

---

### **78. How do you test pagination?**

Create test data for different pages and verify correct data is returned.

**Example:**
```java
@Test
public void testPagination() {
    List<Employee> allEmployees = createTestEmployees(25);
    when(repository.findAll()).thenReturn(allEmployees);
    
    List<Employee> page1 = service.getEmployees(0, 10);
    List<Employee> page2 = service.getEmployees(1, 10);
    
    assertEquals(10, page1.size());
    assertEquals(10, page2.size());
    assertNotEquals(page1.get(0), page2.get(0));
}
```

---

### **79. How do you test sorting functionality?**

Create unsorted test data and verify it comes back sorted.

**Example:**
```java
@Test
public void testEmployeesSortedBySalary() {
    List<Employee> unsorted = Arrays.asList(
        new Employee("John", 50000),
        new Employee("Alice", 70000),
        new Employee("Bob", 60000)
    );
    
    List<Employee> sorted = service.sortBySalary(unsorted);
    
    assertEquals("John", sorted.get(0).getName());
    assertEquals("Bob", sorted.get(1).getName());
    assertEquals("Alice", sorted.get(2).getName());
}
```

---

### **80. How do you test search/filter functionality?**

Create diverse test data and verify filters return correct subsets.

**Example:**
```java
@Test
public void testFilterByDepartment() {
    List<Employee> all = Arrays.asList(
        new Employee("John", "IT"),
        new Employee("Alice", "HR"),
        new Employee("Bob", "IT")
    );
    when(repository.findAll()).thenReturn(all);
    
    List<Employee> itEmployees = service.filterByDepartment("IT");
    
    assertEquals(2, itEmployees.size());
    assertTrue(itEmployees.stream()
        .allMatch(e -> e.getDepartment().equals("IT")));
}
```

---

## **PART 6: Best Practices and Common Mistakes**

### **81. Should tests depend on each other?**

No! Each test should be independent. Test execution order should not matter.

**Example - Bad:**
```java
static Employee savedEmployee;

@Test
public void test1_saveEmployee() {
    savedEmployee = service.save(new Employee("John"));
}

@Test
public void test2_updateEmployee() {
    service.update(savedEmployee); // Depends on test1!
}
```

**Example - Good:**
```java
@Before
public void setup() {
    savedEmployee = service.save(new Employee("John"));
}

@Test
public void testUpdate() {
    service.update(savedEmployee); // Independent
}
```

---

### **82. How long should a unit test be?**

Short and focused! Usually 5-15 lines. If longer, you're probably testing too much in one test.

**Example - Bad:**
```java
@Test
public void testEverything() {
    // 50 lines testing multiple scenarios
}
```

**Example - Good:**
```java
@Test
public void testSave() {
    // 5 lines testing just save
}

@Test
public void testUpdate() {
    // 5 lines testing just update
}
```

---

### **83. Should you test getter and setter methods?**

Generally no - they're too simple. Focus on business logic. Exception: if getters/setters have validation or calculation logic.

**Example - Don't test:**
```java
public void setName(String name) {
    this.name = name;
}
```

**Example - Do test:**
```java
public void setSalary(double salary) {
    if (salary < 0) {
        throw new InvalidSalaryException();
    }
    this.salary = salary;
}
```

---

### **84. What is test data builder pattern?**

A pattern for creating test objects easily without constructor overload.

**Example:**
```java
public class EmployeeBuilder {
    private String name = "Default";
    private int salary = 50000;
    
    public EmployeeBuilder withName(String name) {
        this.name = name;
        return this;
    }
    
    public EmployeeBuilder withSalary(int salary) {
        this.salary = salary;
        return this;
    }
    
    public Employee build() {
        return new Employee(name, salary);
    }
}

@Test
public void test() {
    Employee emp = new EmployeeBuilder()
        .withName("John")
        .withSalary(60000)
        .build();
}
```

---

### **85. What is the AAA pattern in testing?**

**Arrange** - Set up test data
**Act** - Execute the code being tested
**Assert** - Verify results

**Example:**
```java
@Test
public void testCalculateBonus() {
    // Arrange
    Employee emp = new Employee("John", 50000);
    
    // Act
    double bonus = service.calculateBonus(emp);
    
    // Assert
    assertEquals(5000, bonus, 0.01);
}
```

---

### **86. Should you have multiple assertions in one test?**

It depends. Generally, test one logical concept per test, but you may need multiple assertions to fully verify it.

**Example - Acceptable:**
```java
@Test
public void testCreateEmployee() {
    Employee emp = service.createEmployee("John", "IT");
    
    assertNotNull(emp.getId()); // All assertions verify employee creation
    assertEquals("John", emp.getName());
    assertEquals("IT", emp.getDepartment());
    assertTrue(emp.isActive());
}
```

---

### **87. What naming convention should you use for test methods?**

Be descriptive! Common patterns:
- `testMethodName_Scenario_ExpectedResult()`
- `shouldExpectedResult_WhenScenario()`
- `givenScenario_whenAction_thenResult()`

**Example:**
```java
@Test
public void testCalculateScore_WithValidInputs_ReturnsCorrectScore() { }

@Test
public void shouldThrowException_WhenEmployeeNotFound() { }

@Test
public void givenInactiveEmployee_whenCalculatingBonus_thenReturnsZero() { }
```

---

### **88. Should you test implementation details or behavior?**

Test behavior (what), not implementation (how). Tests should survive refactoring.

**Example - Bad (testing implementation):**
```java
@Test
public void testUsesHashMapInternally() {
    verify(service).createHashMap(); // Too specific
}
```

**Example - Good (testing behavior):**
```java
@Test
public void testReturnsUniqueEmployees() {
    List<Employee> result = service.getUniqueEmployees();
    assertEquals(5, result.size()); // Don't care how, just that it works
}
```

---

### **89. What is test pollution?**

When one test affects another test's outcome - usually through shared state.

**Example - Test pollution:**
```java
static List<Employee> employees = new ArrayList<>();

@Test
public void test1() {
    employees.add(new Employee("John"));
}

@Test
public void test2() {
    assertEquals(0, employees.size()); // FAILS! test1 added employee
}
```

**Fix:** Use @Before to reset state or avoid static variables.

---

### **90. Should you test third-party library code?**

No! Trust that Spring, Hibernate, etc. are already tested. Test YOUR code that uses them.

**Example - Don't test:**
```java
@Test
public void testJPARepositorySavesCorrectly() {
    // Testing Spring JPA - unnecessary!
}
```

**Example - Do test:**
```java
@Test
public void testBusinessLogicBeforeSave() {
    service.saveEmployee(employee);
    verify(auditLogger).log(contains("Employee saved")); // Test YOUR logic
}
```

---

### **91. What is the difference between black box and white box testing?**

**Black box** - Test without knowing internal code structure (test as a user would)
**White box** - Test with knowledge of internal code structure

**Example:**
- Black box: Testing calculateScore(80, 90) returns 85 without knowing how
- White box: Testing that calculateScore uses specific formula internally

Most unit tests are white box testing.

---

### **92. How many test cases should you write?**

Cover all important scenarios:
- Happy path (normal case)
- Edge cases (boundary values)
- Error cases (invalid inputs)
- Null/empty inputs

**Example for calculateScore():**
```java
@Test public void testNormalScores() { } // 80, 90
@Test public void testMinimumScores() { } // 0, 0
@Test public void testMaximumScores() { } // 100, 100
@Test public void testNegativeScores() { } // -10, 50
@Test public void testNullInput() { } // null values
```

---

### **93. What is the difference between state verification and behavior verification?**

**State verification** - Check final state of object
**Behavior verification** - Check which methods were called (using verify)

**Example:**
```java
// State verification
Employee emp = service.promoteEmployee(1);
assertEquals("Senior Developer", emp.getPosition());

// Behavior verification
verify(emailService).sendPromotionEmail(emp);
```

---

### **94. Should you mock everything in a test?**

No! Only mock external dependencies. Test the real logic of the class you're testing.

**Example - Over-mocking (bad):**
```java
@Mock private Calculator calculator;

@Test
public void test() {
    when(calculator.add(2, 3)).thenReturn(5); // Why mock simple logic?
}
```

**Example - Good:**
```java
@Mock private EmployeeRepository repository; // Mock database
// Use real Calculator - it's simple logic
```

---

### **95. What is flaky test?**

A test that sometimes passes and sometimes fails without code changes. Usually due to:
- Timing issues
- Random data
- External dependencies
- Test order dependencies

**Example - Flaky:**
```java
@Test
public void test() {
    service.processAsync();
    Thread.sleep(100); // Might not be enough time!
    assertEquals(expected, result);
}
```

Flaky tests are very bad - fix them immediately!

---

### **96. How do you handle tests that need database?**

Use:
- In-memory database (H2) for fast tests
- Test containers for real database testing
- Mock repository for unit tests

**Example:**
```java
// Unit test - mock repository
@Mock
private EmployeeRepository repository;

// Integration test - use H2 database
@RunWith(SpringRunner.class)
@DataJpaTest
public class RepositoryIntegrationTest {
    @Autowired
    private EmployeeRepository repository; // Real H2 database
}
```

---

### **97. What is mutation testing?**

Mutation testing changes your code slightly (mutates) and checks if tests catch the change. Measures test quality.

**Example:**
Original code: `if (score > 80)`
Mutation: `if (score >= 80)`

If your tests still pass, they're not good enough!

Tools: PIT (popular Java mutation testing tool)

---

### **98. Should you test toString(), equals(), and hashCode()?**

Usually no - they're generated or very simple. Exception: if you wrote custom complex logic.

**Example - Don't test:**
```java
@Override
public String toString() {
    return "Employee{name='" + name + "'}"; // Too simple
}
```

**Example - Do test:**
```java
@Override
public boolean equals(Object obj) {
    // Complex business logic for equality
    // Yes, test this!
}
```

---

### **99. How do you test legacy code with no tests?**

Start small:
1. Add tests for new features
2. Add tests before fixing bugs
3. Use characterization tests (test current behavior)
4. Gradually increase coverage

**Example:**
```java
// Legacy method with no tests
public double calculate(int a, int b) {
    // Complex legacy code
}

// Characterization test - document current behavior
@Test
public void testCurrentBehavior() {
    assertEquals(15, calculate(10, 5)); // Document what it does NOW
}
```

Then refactor safely.

---

### **100. What is the test pyramid?**

A testing strategy with different layers:
- **Many unit tests** (70%) - Fast, isolated
- **Some integration tests** (20%) - Test components together
- **Few end-to-end tests** (10%) - Test entire system

**Example for PerformanceTrack:**
- Unit tests: Test ReportService.calculateScore() - hundreds of tests
- Integration tests: Test Controller + Service + Repository - dozens of tests
- E2E tests: Test complete user flow through UI - few tests

---

This completes 100 questions! Would you like me to continue with:
- Spring Boot testing specifics (@MockBean, @WebMvcTest, etc.)
- More advanced scenarios
- Common interview coding exercises
- Troubleshooting and debugging tests

Type **"continue"** for more questions!

# JUnit and Mockito Interview Questions - Continued Part 3

## **PART 7: Spring Boot Testing with JUnit and Mockito**

### **101. What is @SpringBootTest annotation?**

@SpringBootTest loads the complete Spring application context for integration testing. It's like starting your whole application for testing.

**Example:**
```java
@RunWith(SpringRunner.class)
@SpringBootTest
public class ReportServiceIntegrationTest {
    @Autowired
    private ReportService reportService; // Real bean from context
    
    @Test
    public void testCompleteFlow() {
        // Tests with real Spring beans
    }
}
```

Use this for integration tests, not unit tests (it's slow).

---

### **102. What is the difference between @Mock and @MockBean?**

**@Mock** - Mockito annotation, creates mock for unit tests
**@MockBean** - Spring Boot annotation, creates mock and adds it to Spring context

**Example:**
```java
// Unit test - plain Mockito
@Mock
private ReportRepository repository;

// Spring Boot integration test
@SpringBootTest
public class ServiceTest {
    @MockBean
    private ReportRepository repository; // Replaces real bean in context
    
    @Autowired
    private ReportService service; // Gets the mocked repository injected
}
```

---

### **103. What is @WebMvcTest?**

@WebMvcTest loads only the web layer (controllers) for testing REST APIs. Much faster than @SpringBootTest.

**Example:**
```java
@RunWith(SpringRunner.class)
@WebMvcTest(ReportController.class)
public class ReportControllerTest {
    @Autowired
    private MockMvc mockMvc; // For simulating HTTP requests
    
    @MockBean
    private ReportService reportService; // Mock the service
    
    @Test
    public void testGetReport() throws Exception {
        mockMvc.perform(get("/api/reports/1"))
            .andExpect(status().isOk());
    }
}
```

---

### **104. What is MockMvc?**

MockMvc simulates HTTP requests to your controllers without starting a real server. Used for testing REST APIs.

**Example:**
```java
@Test
public void testCreateReport() throws Exception {
    Report report = new Report("Test Report");
    
    mockMvc.perform(post("/api/reports")
            .contentType(MediaType.APPLICATION_JSON)
            .content(toJson(report)))
        .andExpect(status().isCreated())
        .andExpect(jsonPath("$.name").value("Test Report"));
}
```

---

### **105. What is @DataJpaTest?**

@DataJpaTest loads only JPA-related components for testing repositories. Uses in-memory database by default.

**Example:**
```java
@RunWith(SpringRunner.class)
@DataJpaTest
public class ReportRepositoryTest {
    @Autowired
    private ReportRepository reportRepository;
    
    @Test
    public void testFindByEmployeeId() {
        Report report = new Report();
        reportRepository.save(report);
        
        List<Report> found = reportRepository.findByEmployeeId(1);
        assertEquals(1, found.size());
    }
}
```

---

### **106. What is TestEntityManager?**

TestEntityManager is a helper for JPA tests in @DataJpaTest. It provides convenient methods for test data setup.

**Example:**
```java
@DataJpaTest
public class RepositoryTest {
    @Autowired
    private TestEntityManager entityManager;
    
    @Autowired
    private EmployeeRepository repository;
    
    @Test
    public void test() {
        Employee emp = new Employee("John");
        entityManager.persist(emp); // Save test data
        entityManager.flush();
        
        Employee found = repository.findById(emp.getId()).get();
        assertEquals("John", found.getName());
    }
}
```

---

### **107. What is @AutoConfigureMockMvc?**

When used with @SpringBootTest, it auto-configures MockMvc so you can test controllers with the full application context.

**Example:**
```java
@RunWith(SpringRunner.class)
@SpringBootTest
@AutoConfigureMockMvc
public class IntegrationTest {
    @Autowired
    private MockMvc mockMvc; // Available for testing
    
    @Test
    public void testEndToEnd() throws Exception {
        mockMvc.perform(get("/api/reports"))
            .andExpect(status().isOk());
    }
}
```

---

### **108. How do you test a REST controller with authentication?**

Use MockMvc with security testing support to mock authenticated users.

**Example:**
```java
@Test
@WithMockUser(username = "john", roles = "MANAGER")
public void testSecuredEndpoint() throws Exception {
    mockMvc.perform(get("/api/reports"))
        .andExpect(status().isOk()); // Authenticated as john
}

@Test
public void testWithoutAuth() throws Exception {
    mockMvc.perform(get("/api/reports"))
        .andExpect(status().isUnauthorized()); // No authentication
}
```

---

### **109. What is @TestConfiguration?**

@TestConfiguration creates Spring beans only for tests. Useful for test-specific configuration.

**Example:**
```java
@TestConfiguration
public class TestConfig {
    @Bean
    public DateProvider dateProvider() {
        return new FixedDateProvider(); // Returns fixed date for testing
    }
}

@RunWith(SpringRunner.class)
@SpringBootTest
@Import(TestConfig.class)
public class ServiceTest {
    // Uses FixedDateProvider instead of real one
}
```

---

### **110. How do you test application properties in tests?**

Use @TestPropertySource to override properties for tests.

**Example:**
```java
@RunWith(SpringRunner.class)
@SpringBootTest
@TestPropertySource(properties = {
    "app.email.enabled=false",
    "app.report.limit=100"
})
public class ServiceTest {
    // Tests run with these property values
}
```

---

### **111. What is @Sql annotation?**

@Sql executes SQL scripts before tests. Useful for setting up test data.

**Example:**
```java
@RunWith(SpringRunner.class)
@DataJpaTest
@Sql("/test-data.sql") // Runs this script before tests
public class RepositoryTest {
    @Test
    public void testWithData() {
        // test-data.sql already populated database
    }
}
```

test-data.sql:
```sql
INSERT INTO employee (id, name) VALUES (1, 'John');
INSERT INTO employee (id, name) VALUES (2, 'Alice');
```

---

### **112. What is the difference between @SpringBootTest and @WebMvcTest?**

**@SpringBootTest** - Loads entire application, slow, for integration tests
**@WebMvcTest** - Loads only web layer, fast, for controller tests

**Example:**
```java
// Integration test - full app
@SpringBootTest
public class FullAppTest { }

// Controller test - web layer only
@WebMvcTest(ReportController.class)
public class ControllerOnlyTest { }
```

Use @WebMvcTest when possible - it's much faster!

---

### **113. How do you test JSON responses in Spring Boot?**

Use JsonPath with MockMvc to verify JSON structure and values.

**Example:**
```java
@Test
public void testJsonResponse() throws Exception {
    mockMvc.perform(get("/api/reports/1"))
        .andExpect(status().isOk())
        .andExpect(jsonPath("$.id").value(1))
        .andExpect(jsonPath("$.name").value("Q4 Report"))
        .andExpect(jsonPath("$.employee.name").value("John"));
}
```

---

### **114. What is @ActiveProfiles in testing?**

@ActiveProfiles activates specific Spring profiles for tests.

**Example:**
```java
@RunWith(SpringRunner.class)
@SpringBootTest
@ActiveProfiles("test") // Uses application-test.properties
public class ServiceTest {
    // Tests run with 'test' profile configuration
}
```

application-test.properties:
```
spring.datasource.url=jdbc:h2:mem:testdb
logging.level.root=DEBUG
```

---

### **115. How do you test file upload endpoints?**

Use MockMultipartFile to simulate file uploads.

**Example:**
```java
@Test
public void testFileUpload() throws Exception {
    MockMultipartFile file = new MockMultipartFile(
        "file",
        "report.pdf",
        MediaType.APPLICATION_PDF_VALUE,
        "PDF content".getBytes()
    );
    
    mockMvc.perform(multipart("/api/reports/upload")
            .file(file))
        .andExpect(status().isOk());
}
```

---

### **116. What is @DirtiesContext?**

@DirtiesContext tells Spring to reload the application context after the test. Use sparingly - it slows tests.

**Example:**
```java
@Test
@DirtiesContext // Context reloaded after this test
public void testThatModifiesApplicationState() {
    // This test changes global state
    cacheService.clearAll();
}
```

Better to avoid needing this by writing independent tests.

---

### **117. How do you test scheduled tasks (@Scheduled methods)?**

Trigger them manually in tests or use Awaitility to wait for execution.

**Example:**
```java
@Component
public class ReportScheduler {
    @Scheduled(cron = "0 0 1 * * ?")
    public void generateDailyReport() {
        // Generate report
    }
}

@Test
public void testScheduledTask() {
    // Trigger manually
    scheduler.generateDailyReport();
    
    verify(reportService).generate(any());
}
```

---

### **118. How do you test async methods (@Async)?**

Use CompletableFuture or callbacks, wait for completion in tests.

**Example:**
```java
@Test
public void testAsyncMethod() throws Exception {
    CompletableFuture<Report> future = reportService.generateAsync(1);
    
    Report report = future.get(5, TimeUnit.SECONDS); // Wait max 5 seconds
    
    assertNotNull(report);
    assertEquals("Generated", report.getStatus());
}
```

---

### **119. What is @MockitoSettings?**

@MockitoSettings configures Mockito behavior for a test class.

**Example:**
```java
@MockitoSettings(strictness = Strictness.LENIENT)
public class ServiceTest {
    // All mocks in this class use lenient stubbing
}
```

Useful for controlling strictness of mock verification.

---

### **120. How do you test exception handling in controllers?**

Use MockMvc to verify HTTP error responses and exception handlers.

**Example:**
```java
@Test
public void testNotFoundHandler() throws Exception {
    when(reportService.getReport(999))
        .thenThrow(new ReportNotFoundException());
    
    mockMvc.perform(get("/api/reports/999"))
        .andExpect(status().isNotFound())
        .andExpect(jsonPath("$.message").value("Report not found"));
}
```

---

## **PART 8: Advanced Mockito Patterns**

### **121. What is Answer in Mockito?**

Answer lets you define custom behavior when a method is called - more flexible than thenReturn.

**Example:**
```java
when(calculator.add(anyInt(), anyInt())).thenAnswer(new Answer<Integer>() {
    public Integer answer(InvocationOnMock invocation) {
        int a = invocation.getArgument(0);
        int b = invocation.getArgument(1);
        System.out.println("Adding " + a + " + " + b);
        return a + b;
    }
});
```

Useful for logging, complex calculations, or callbacks.

---

### **122. What is BDDMockito?**

BDDMockito provides Behavior-Driven Development style syntax - more readable in some cases.

**Example:**
```java
// Traditional Mockito
when(service.getReport(1)).thenReturn(report);

// BDDMockito - reads like plain English
given(service.getReport(1)).willReturn(report);

// Verification
verify(service).getReport(1);

// BDD style
then(service).should().getReport(1);
```

Same functionality, different syntax style.

---

### **123. How do you mock a method that returns a stream?**

Return a stream from mock just like any other return value.

**Example:**
```java
List<Employee> employees = Arrays.asList(emp1, emp2, emp3);
when(repository.findAll()).thenReturn(employees);

Stream<Employee> stream = repository.findAll().stream();
// Now you can test stream operations
```

Or mock the stream directly if needed.

---

### **124. How do you mock a chain of method calls?**

Use RETURNS_DEEP_STUBS or stub each method in the chain.

**Example:**
```java
// Without deep stubs - verbose
Report report = mock(Report.class);
Employee employee = mock(Employee.class);
when(report.getEmployee()).thenReturn(employee);
when(employee.getName()).thenReturn("John");

// With deep stubs - simpler
Report report = mock(Report.class, RETURNS_DEEP_STUBS);
when(report.getEmployee().getName()).thenReturn("John");
```

---

### **125. What is doAnswer() vs thenAnswer()?**

They're the same, just different syntax:
**thenAnswer()** - used with when()
**doAnswer()** - used with do...when() style

**Example:**
```java
// thenAnswer style
when(service.process()).thenAnswer(invocation -> "result");

// doAnswer style
doAnswer(invocation -> "result").when(service).process();
```

Use doAnswer for void methods or spies.

---

### **126. How do you verify method was never called with specific argument?**

Use verify with never() and argument matcher.

**Example:**
```java
service.processReports(reports);

// Verify deleteReport was NEVER called with report ID 5
verify(service, never()).deleteReport(5);

// Verify email was NEVER sent to admin
verify(emailService, never()).send(eq("admin@company.com"), anyString());
```

---

### **127. What is ArgumentMatcher and how do you create custom matchers?**

ArgumentMatcher lets you create custom argument matching logic.

**Example:**
```java
class EmployeeNameMatcher implements ArgumentMatcher<Employee> {
    private String expectedName;
    
    public EmployeeNameMatcher(String name) {
        this.expectedName = name;
    }
    
    public boolean matches(Employee employee) {
        return employee != null && 
               expectedName.equals(employee.getName());
    }
}

@Test
public void test() {
    service.save(employee);
    verify(repository).save(argThat(new EmployeeNameMatcher("John")));
}
```

---

### **128. How do you mock static methods in Mockito 3.4+?**

Use mockStatic() to create a mock of a static method context.

**Example:**
```java
@Test
public void testStaticMethod() {
    try (MockedStatic<Utils> utilities = mockStatic(Utils.class)) {
        utilities.when(() -> Utils.getCurrentDate())
                 .thenReturn(fixedDate);
        
        // Now Utils.getCurrentDate() returns fixedDate
        service.process();
        
        utilities.verify(() -> Utils.getCurrentDate());
    } // Mock automatically closed
}
```

Use try-with-resources to ensure cleanup.

---

### **129. What is Mockito's strict stubbing?**

Strict stubbing detects unnecessary stubbings and helps keep tests clean. Enabled by default in newer Mockito.

**Example:**
```java
@Test
public void test() {
    when(repository.findById(1)).thenReturn(employee); // Stubbed
    when(repository.count()).thenReturn(10); // Stubbed but NEVER used!
    
    service.getEmployee(1); // Only uses findById
    
    // Strict stubbing will report unused count() stubbing
}
```

Helps identify dead test code.

---

### **130. How do you handle Mockito's UnstubbedMethodException?**

This happens with strict mocks when calling an unstubbed method. Either stub the method or use lenient stubbing.

**Example:**
```java
// Problem
@Test
public void test() {
    String name = mockEmployee.getName(); // Not stubbed - exception!
}

// Solution 1: Stub it
when(mockEmployee.getName()).thenReturn("John");

// Solution 2: Lenient mock
lenient().when(mockEmployee.getName()).thenReturn("John");
```

---

### **131. What is the difference between mock, spy, and real object?**

**Mock** - Completely fake, no real code executes
**Spy** - Real object, real code executes unless stubbed
**Real** - Actual object with no mocking

**Example:**
```java
// Mock - fake calculator
Calculator mock = mock(Calculator.class);
when(mock.add(2, 3)).thenReturn(100); // Returns 100, not 5!

// Spy - real calculator
Calculator spy = spy(new Calculator());
int result = spy.add(2, 3); // Returns 5 (real calculation)

// Real - normal object
Calculator real = new Calculator();
int result = real.add(2, 3); // Returns 5
```

---

### **132. How do you test callbacks or listeners?**

Capture the callback using ArgumentCaptor and invoke it in your test.

**Example:**
```java
@Test
public void testCallback() {
    ArgumentCaptor<ReportCallback> captor = 
        ArgumentCaptor.forClass(ReportCallback.class);
    
    service.generateReportAsync(1, callback);
    
    verify(asyncProcessor).process(eq(1), captor.capture());
    
    ReportCallback callback = captor.getValue();
    callback.onComplete(report); // Invoke callback
    
    // Verify what happens after callback
    verify(notificationService).notify(report);
}
```

---

### **133. How do you mock builder pattern objects?**

Mock each method in the chain to return the mock itself.

**Example:**
```java
ReportBuilder builder = mock(ReportBuilder.class);
when(builder.withName(anyString())).thenReturn(builder);
when(builder.withDate(any())).thenReturn(builder);
when(builder.build()).thenReturn(report);

// Now this works
Report result = builder
    .withName("Q4 Report")
    .withDate(date)
    .build();
```

---

### **134. What is the difference between mock() with answer parameter?**

You can provide a default answer strategy when creating mocks.

**Example:**
```java
// Default answer returns null/0/false
Employee mock1 = mock(Employee.class);

// Custom default answer
Employee mock2 = mock(Employee.class, invocation -> "default");

// Returns deep stubs
Employee mock3 = mock(Employee.class, RETURNS_DEEP_STUBS);

// Returns smart nulls (better error messages)
Employee mock4 = mock(Employee.class, RETURNS_SMART_NULLS);
```

---

### **135. How do you verify no interactions except specific ones?**

Use verifyNoMoreInteractions() after verifying expected interactions.

**Example:**
```java
@Test
public void testOnlySaveCalled() {
    service.createReport(report);
    
    verify(repository).save(report); // Expected
    verifyNoMoreInteractions(repository); // Nothing else should be called
}
```

---

### **136. What is the difference between times(0) and never()?**

They're the same! never() is just more readable.

**Example:**
```java
verify(service, times(0)).delete(any()); // Works
verify(service, never()).delete(any()); // Same, more readable
```

---

### **137. How do you mock System.out.println() for testing?**

Redirect System.out to a ByteArrayOutputStream.

**Example:**
```java
@Test
public void testConsoleOutput() {
    ByteArrayOutputStream output = new ByteArrayOutputStream();
    System.setOut(new PrintStream(output));
    
    service.printReport(); // Prints to console
    
    String printed = output.toString();
    assertTrue(printed.contains("Report Generated"));
    
    System.setOut(System.out); // Reset
}
```

---

### **138. How do you test code that uses Thread.sleep()?**

Extract sleep into a mockable component or use Awaitility library.

**Example:**
```java
public class ReportService {
    private Sleeper sleeper;
    
    public void delayedProcess() {
        sleeper.sleep(1000); // Mockable
        process();
    }
}

@Test
public void test() {
    doNothing().when(sleeper).sleep(anyLong()); // No actual sleep
    service.delayedProcess(); // Runs instantly
}
```

---

### **139. What is MockingDetails?**

MockingDetails provides information about a mock object.

**Example:**
```java
Employee employee = mock(Employee.class);

MockingDetails details = mockingDetails(employee);
assertTrue(details.isMock()); // true
assertFalse(details.isSpy()); // false

// Check if object is a mock
if (mockingDetails(someObject).isMock()) {
    // It's a mock
}
```

---

### **140. How do you handle equals() and hashCode() in mocks?**

Mocks don't override equals/hashCode by default. Use custom implementation if needed.

**Example:**
```java
Employee mock1 = mock(Employee.class);
Employee mock2 = mock(Employee.class);

assertNotEquals(mock1, mock2); // Different objects

// If you need specific equals behavior
when(mock1.equals(any())).thenReturn(true);
```

Usually not needed - avoid comparing mocks.

---

## **PART 9: Real Interview Scenarios**

### **141. How would you test a method that processes a large list?**

Create a smaller representative test list. Focus on logic, not performance.

**Example:**
```java
@Test
public void testBulkProcessing() {
    List<Employee> employees = Arrays.asList(
        new Employee("John", 50000),
        new Employee("Alice", 60000),
        new Employee("Bob", 55000)
    );
    
    List<Report> reports = service.generateReports(employees);
    
    assertEquals(3, reports.size());
    assertTrue(reports.stream()
        .allMatch(r -> r.getStatus().equals("Generated")));
}
```

For performance tests, use separate performance test suite.

---

### **142. How do you test a singleton class?**

Make singleton testable by adding a reset method or use dependency injection instead.

**Example:**
```java
public class ConfigManager {
    private static ConfigManager instance;
    
    public static ConfigManager getInstance() {
        if (instance == null) {
            instance = new ConfigManager();
        }
        return instance;
    }
    
    // For testing only
    static void resetForTest() {
        instance = null;
    }
}

@After
public void cleanup() {
    ConfigManager.resetForTest();
}
```

Better: Avoid singletons, use Spring beans.

---

### **143. How would you test database transactions?**

Use @Transactional in tests (auto-rollback) or verify transaction methods are called.

**Example:**
```java
@Test
@Transactional // Auto rollback after test
public void testTransactionalOperation() {
    service.saveMultipleReports(reports);
    
    List<Report> saved = repository.findAll();
    assertEquals(3, saved.size());
} // Transaction rolled back, database unchanged

// Or mock transaction manager
@Test
public void testTransactionCommit() {
    service.saveWithTransaction(report);
    verify(transactionManager).commit(any());
}
```

---

### **144. How do you test a method with multiple dependencies?**

Mock all dependencies and verify interactions separately.

**Example:**
```java
public class ReportService {
    private EmployeeRepository empRepo;
    private GoalRepository goalRepo;
    private EmailService emailService;
    private AuditLogger auditLogger;
    
    public void generateReport(int empId) {
        Employee emp = empRepo.findById(empId);
        List<Goal> goals = goalRepo.findByEmployee(empId);
        Report report = create(emp, goals);
        emailService.send(emp.getEmail(), report);
        auditLogger.log("Report generated");
    }
}

@Test
public void testGenerateReport() {
    when(empRepo.findById(1)).thenReturn(employee);
    when(goalRepo.findByEmployee(1)).thenReturn(goals);
    
    service.generateReport(1);
    
    verify(emailService).send(anyString(), any());
    verify(auditLogger).log(contains("generated"));
}
```

---

### **145. How would you test a method that calls external API?**

Mock the HTTP client or create a fake API wrapper.

**Example:**
```java
@Mock
private RestTemplate restTemplate;

@Test
public void testExternalApiCall() {
    ExternalReport response = new ExternalReport("data");
    when(restTemplate.getForObject(anyString(), eq(ExternalReport.class)))
        .thenReturn(response);
    
    Report result = service.fetchFromExternal(1);
    
    assertNotNull(result);
    verify(restTemplate).getForObject(contains("/api/reports/"), any());
}
```

---

### **146. How do you test pagination correctly?**

Test boundary cases: first page, last page, middle page, empty results.

**Example:**
```java
@Test
public void testFirstPage() {
    List<Report> page = service.getReports(0, 10);
    assertEquals(10, page.size());
}

@Test
public void testLastPage() {
    List<Report> page = service.getReports(9, 10); // 95 total records
    assertEquals(5, page.size()); // Only 5 in last page
}

@Test
public void testEmptyPage() {
    List<Report> page = service.getReports(100, 10);
    assertTrue(page.isEmpty());
}
```

---

### **147. How would you test a caching mechanism?**

Verify cached data is returned on second call without hitting repository.

**Example:**
```java
@Test
public void testCaching() {
    when(repository.findById(1)).thenReturn(employee);
    
    Employee first = service.getEmployee(1);
    Employee second = service.getEmployee(1); // Should use cache
    
    assertSame(first, second); // Same object reference
    verify(repository, times(1)).findById(1); // Called only once!
}
```

---

### **148. How do you test validation logic?**

Test each validation rule separately - valid cases and invalid cases.

**Example:**
```java
@Test
public void testValidEmployee() {
    Employee emp = new Employee("John", "john@company.com", 50000);
    assertTrue(validator.isValid(emp));
}

@Test(expected = InvalidEmailException.class)
public void testInvalidEmail() {
    Employee emp = new Employee("John", "invalid-email", 50000);
    validator.validate(emp); // Should throw
}

@Test(expected = InvalidSalaryException.class)
public void testNegativeSalary() {
    Employee emp = new Employee("John", "john@company.com", -1000);
    validator.validate(emp);
}
```

---

### **149. How would you test logging?**

Mock the logger and verify log methods are called with correct messages.

**Example:**
```java
@Mock
private Logger logger;

@Test
public void testErrorLogging() {
    when(repository.findById(999)).thenThrow(new DataException());
    
    try {
        service.getReport(999);
    } catch (Exception e) {
        // Expected
    }
    
    verify(logger).error(contains("Report not found"), any(Exception.class));
}
```

---

### **150. How do you test retry logic?**

Stub to fail multiple times then succeed, verify retry attempts.

**Example:**
```java
@Test
public void testRetryMechanism() {
    when(repository.save(any()))
        .thenThrow(new TransientException())
        .thenThrow(new TransientException())
        .thenReturn(employee); // Third attempt succeeds
    
    Employee result = service.saveWithRetry(employee);
    
    assertNotNull(result);
    verify(repository, times(3)).save(any()); // Tried 3 times
}
```

---

I've now covered 150 comprehensive questions! 

**Should I continue with:**
- Common coding exercises in interviews
- Debugging and troubleshooting test failures
- Performance testing considerations
- Test organization and naming strategies
- More real-world scenarios from your PerformanceTrack project

Type **"continue"** for more!

# JUnit and Mockito Interview Questions - Continued Part 4

## **PART 10: Common Coding Exercises & Practical Scenarios**

### **151. Write a test for calculating employee bonus based on performance score**

This is a very common interview question - testing business logic with multiple conditions.

**Example:**
```java
// Business Logic: 
// Score >= 90: 20% bonus
// Score >= 75: 15% bonus
// Score >= 60: 10% bonus
// Score < 60: 5% bonus

@Test
public void testBonusForExcellentPerformance() {
    Employee emp = new Employee("John", 50000);
    emp.setPerformanceScore(95);
    
    double bonus = service.calculateBonus(emp);
    
    assertEquals(10000, bonus, 0.01); // 20% of 50000
}

@Test
public void testBonusForGoodPerformance() {
    Employee emp = new Employee("Alice", 60000);
    emp.setPerformanceScore(80);
    
    double bonus = service.calculateBonus(emp);
    
    assertEquals(9000, bonus, 0.01); // 15% of 60000
}

@Test
public void testBonusForAveragePerformance() {
    Employee emp = new Employee("Bob", 40000);
    emp.setPerformanceScore(65);
    
    double bonus = service.calculateBonus(emp);
    
    assertEquals(4000, bonus, 0.01); // 10% of 40000
}

@Test
public void testBonusForPoorPerformance() {
    Employee emp = new Employee("Mike", 55000);
    emp.setPerformanceScore(50);
    
    double bonus = service.calculateBonus(emp);
    
    assertEquals(2750, bonus, 0.01); // 5% of 55000
}

@Test
public void testBoundaryScore90() {
    Employee emp = new Employee("Test", 50000);
    emp.setPerformanceScore(90);
    
    double bonus = service.calculateBonus(emp);
    
    assertEquals(10000, bonus, 0.01); // Exactly 90 gets 20%
}
```

---

### **152. Write a test for a service that filters employees by department**

Testing filtering and collection operations.

**Example:**
```java
@Test
public void testFilterByDepartment() {
    List<Employee> allEmployees = Arrays.asList(
        new Employee("John", "IT"),
        new Employee("Alice", "HR"),
        new Employee("Bob", "IT"),
        new Employee("Carol", "Finance"),
        new Employee("Dave", "IT")
    );
    
    when(repository.findAll()).thenReturn(allEmployees);
    
    List<Employee> itEmployees = service.getEmployeesByDepartment("IT");
    
    assertEquals(3, itEmployees.size());
    assertTrue(itEmployees.stream()
        .allMatch(e -> "IT".equals(e.getDepartment())));
}

@Test
public void testFilterNonExistentDepartment() {
    when(repository.findAll()).thenReturn(employees);
    
    List<Employee> result = service.getEmployeesByDepartment("Marketing");
    
    assertTrue(result.isEmpty());
}

@Test
public void testFilterWithEmptyList() {
    when(repository.findAll()).thenReturn(new ArrayList<>());
    
    List<Employee> result = service.getEmployeesByDepartment("IT");
    
    assertTrue(result.isEmpty());
}
```

---

### **153. Write a test for updating employee salary with validation**

Testing update operations with constraints.

**Example:**
```java
@Test
public void testSuccessfulSalaryUpdate() {
    Employee emp = new Employee("John", 50000);
    when(repository.findById(1)).thenReturn(emp);
    
    service.updateSalary(1, 60000);
    
    assertEquals(60000, emp.getSalary(), 0.01);
    verify(repository).save(emp);
}

@Test(expected = InvalidSalaryException.class)
public void testNegativeSalaryRejected() {
    Employee emp = new Employee("John", 50000);
    when(repository.findById(1)).thenReturn(emp);
    
    service.updateSalary(1, -1000); // Should throw exception
}

@Test(expected = InvalidSalaryException.class)
public void testSalaryDecreaseTooLarge() {
    Employee emp = new Employee("John", 50000);
    when(repository.findById(1)).thenReturn(emp);
    
    // Business rule: Can't decrease salary by more than 20%
    service.updateSalary(1, 30000); // 40% decrease - should fail
}

@Test
public void testSalaryIncreaseNoLimit() {
    Employee emp = new Employee("John", 50000);
    when(repository.findById(1)).thenReturn(emp);
    
    service.updateSalary(1, 100000); // 100% increase - allowed
    
    assertEquals(100000, emp.getSalary(), 0.01);
}
```

---

### **154. Write a test for a service that sends notifications only to active employees**

Testing conditional logic with side effects.

**Example:**
```java
@Test
public void testNotifyActiveEmployeesOnly() {
    List<Employee> employees = Arrays.asList(
        createEmployee("John", true),  // Active
        createEmployee("Alice", false), // Inactive
        createEmployee("Bob", true),    // Active
        createEmployee("Carol", false)  // Inactive
    );
    
    when(repository.findAll()).thenReturn(employees);
    
    service.notifyAllActiveEmployees("Performance review time");
    
    // Should send only 2 emails (to active employees)
    verify(emailService, times(2)).send(anyString(), anyString());
    verify(emailService).send("john@company.com", anyString());
    verify(emailService).send("bob@company.com", anyString());
    verify(emailService, never()).send("alice@company.com", anyString());
    verify(emailService, never()).send("carol@company.com", anyString());
}

private Employee createEmployee(String name, boolean active) {
    Employee emp = new Employee(name);
    emp.setActive(active);
    emp.setEmail(name.toLowerCase() + "@company.com");
    return emp;
}
```

---

### **155. Write a test for calculating average score of completed goals**

Testing calculations with filtering.

**Example:**
```java
@Test
public void testAverageScoreOfCompletedGoals() {
    List<Goal> goals = Arrays.asList(
        createGoal("Goal 1", 80, true),  // Completed
        createGoal("Goal 2", 90, true),  // Completed
        createGoal("Goal 3", 70, false), // Not completed - ignore
        createGoal("Goal 4", 85, true),  // Completed
        createGoal("Goal 5", 60, false)  // Not completed - ignore
    );
    
    when(repository.findByEmployeeId(1)).thenReturn(goals);
    
    double average = service.calculateAverageCompletedScore(1);
    
    // Average of 80, 90, 85 = 85
    assertEquals(85.0, average, 0.01);
}

@Test
public void testAverageWithNoCompletedGoals() {
    List<Goal> goals = Arrays.asList(
        createGoal("Goal 1", 80, false),
        createGoal("Goal 2", 90, false)
    );
    
    when(repository.findByEmployeeId(1)).thenReturn(goals);
    
    double average = service.calculateAverageCompletedScore(1);
    
    assertEquals(0.0, average, 0.01); // Or throw exception, depends on requirements
}

private Goal createGoal(String name, int score, boolean completed) {
    Goal goal = new Goal(name);
    goal.setScore(score);
    goal.setCompleted(completed);
    return goal;
}
```

---

### **156. Write a test for a method that finds top N performers**

Testing sorting and limiting results.

**Example:**
```java
@Test
public void testGetTopPerformers() {
    List<Employee> employees = Arrays.asList(
        createEmployee("John", 95),
        createEmployee("Alice", 88),
        createEmployee("Bob", 92),
        createEmployee("Carol", 78),
        createEmployee("Dave", 85)
    );
    
    when(repository.findAll()).thenReturn(employees);
    
    List<Employee> topThree = service.getTopPerformers(3);
    
    assertEquals(3, topThree.size());
    assertEquals("John", topThree.get(0).getName());  // Score 95
    assertEquals("Bob", topThree.get(1).getName());   // Score 92
    assertEquals("Alice", topThree.get(2).getName()); // Score 88
}

@Test
public void testGetTopPerformersWhenLessThanRequested() {
    List<Employee> employees = Arrays.asList(
        createEmployee("John", 95),
        createEmployee("Alice", 88)
    );
    
    when(repository.findAll()).thenReturn(employees);
    
    List<Employee> topFive = service.getTopPerformers(5);
    
    assertEquals(2, topFive.size()); // Only 2 available
}

private Employee createEmployee(String name, int score) {
    Employee emp = new Employee(name);
    emp.setPerformanceScore(score);
    return emp;
}
```

---

### **157. Write a test for a method that promotes employees based on tenure and performance**

Testing complex business rules.

**Example:**
```java
// Business Rule:
// Tenure >= 5 years AND Score >= 85: Promote to Senior
// Tenure >= 3 years AND Score >= 90: Promote to Senior
// Otherwise: No promotion

@Test
public void testPromotionByTenureAndScore() {
    Employee emp1 = createEmployee("John", 6, 87);  // Qualifies: 5+ years, 85+ score
    Employee emp2 = createEmployee("Alice", 4, 92); // Qualifies: 3+ years, 90+ score
    Employee emp3 = createEmployee("Bob", 6, 80);   // Doesn't qualify: score too low
    Employee emp4 = createEmployee("Carol", 2, 95); // Doesn't qualify: tenure too low
    
    when(repository.findById(1)).thenReturn(emp1);
    
    boolean promoted = service.evaluateForPromotion(1);
    
    assertTrue(promoted);
    assertEquals("Senior Developer", emp1.getPosition());
    verify(repository).save(emp1);
}

@Test
public void testNoPromotionForLowScore() {
    Employee emp = createEmployee("Bob", 6, 80);
    when(repository.findById(1)).thenReturn(emp);
    
    boolean promoted = service.evaluateForPromotion(1);
    
    assertFalse(promoted);
    assertEquals("Developer", emp.getPosition()); // Position unchanged
    verify(repository, never()).save(any());
}

private Employee createEmployee(String name, int tenure, int score) {
    Employee emp = new Employee(name);
    emp.setTenure(tenure);
    emp.setPerformanceScore(score);
    emp.setPosition("Developer");
    return emp;
}
```

---

### **158. Write a test for a report generator that aggregates data from multiple sources**

Testing integration of multiple dependencies.

**Example:**
```java
@Test
public void testGenerateComprehensiveReport() {
    Employee employee = new Employee("John");
    List<Goal> goals = Arrays.asList(
        createGoal("Q1 Goal", 85),
        createGoal("Q2 Goal", 90)
    );
    List<Review> reviews = Arrays.asList(
        createReview("Mid-year", 88),
        createReview("Annual", 92)
    );
    
    when(employeeRepository.findById(1)).thenReturn(employee);
    when(goalRepository.findByEmployeeId(1)).thenReturn(goals);
    when(reviewRepository.findByEmployeeId(1)).thenReturn(reviews);
    
    Report report = service.generateComprehensiveReport(1);
    
    assertNotNull(report);
    assertEquals("John", report.getEmployeeName());
    assertEquals(87.5, report.getAverageGoalScore(), 0.01); // (85+90)/2
    assertEquals(90.0, report.getAverageReviewScore(), 0.01); // (88+92)/2
    
    verify(employeeRepository).findById(1);
    verify(goalRepository).findByEmployeeId(1);
    verify(reviewRepository).findByEmployeeId(1);
}

@Test
public void testReportWithMissingData() {
    Employee employee = new Employee("John");
    when(employeeRepository.findById(1)).thenReturn(employee);
    when(goalRepository.findByEmployeeId(1)).thenReturn(new ArrayList<>());
    when(reviewRepository.findByEmployeeId(1)).thenReturn(new ArrayList<>());
    
    Report report = service.generateComprehensiveReport(1);
    
    assertNotNull(report);
    assertEquals(0.0, report.getAverageGoalScore(), 0.01);
    assertEquals(0.0, report.getAverageReviewScore(), 0.01);
}
```

---

### **159. Write a test for a bulk operation with partial failures**

Testing error handling in batch operations.

**Example:**
```java
@Test
public void testBulkSaveWithPartialFailure() {
    Employee emp1 = new Employee("John");
    Employee emp2 = new Employee("Alice");
    Employee emp3 = new Employee("Bob");
    
    List<Employee> employees = Arrays.asList(emp1, emp2, emp3);
    
    // First and third save successfully, second fails
    when(repository.save(emp1)).thenReturn(emp1);
    when(repository.save(emp2)).thenThrow(new DataException("Duplicate email"));
    when(repository.save(emp3)).thenReturn(emp3);
    
    BulkSaveResult result = service.bulkSave(employees);
    
    assertEquals(2, result.getSuccessCount());
    assertEquals(1, result.getFailureCount());
    assertTrue(result.getFailedEmployees().contains(emp2));
    verify(repository, times(3)).save(any());
    verify(auditLogger).log(contains("Bulk save completed: 2 success, 1 failed"));
}
```

---

### **160. Write a test for checking if an employee is eligible for annual review**

Testing date-based logic.

**Example:**
```java
@Test
public void testEmployeeEligibleForReview() {
    Employee emp = new Employee("John");
    emp.setJoiningDate(parseDate("2023-01-15"));
    
    // Mock current date as 2024-02-01
    when(dateProvider.getCurrentDate()).thenReturn(parseDate("2024-02-01"));
    
    boolean eligible = service.isEligibleForAnnualReview(emp);
    
    assertTrue(eligible); // More than 1 year
}

@Test
public void testNewEmployeeNotEligible() {
    Employee emp = new Employee("Alice");
    emp.setJoiningDate(parseDate("2024-01-01"));
    
    when(dateProvider.getCurrentDate()).thenReturn(parseDate("2024-02-01"));
    
    boolean eligible = service.isEligibleForAnnualReview(emp);
    
    assertFalse(eligible); // Less than 1 year
}

@Test
public void testExactlyOneYearEligible() {
    Employee emp = new Employee("Bob");
    emp.setJoiningDate(parseDate("2023-02-01"));
    
    when(dateProvider.getCurrentDate()).thenReturn(parseDate("2024-02-01"));
    
    boolean eligible = service.isEligibleForAnnualReview(emp);
    
    assertTrue(eligible); // Exactly 1 year - eligible
}

private Date parseDate(String dateStr) {
    // Parse date string to Date object
    // Simplified for example
    return new Date();
}
```

---

## **PART 11: Debugging and Troubleshooting Tests**

### **161. What does "NullPointerException in test" usually mean?**

Usually means a mock wasn't initialized or stubbing is missing.

**Common causes:**
```java
// Cause 1: Forgot to initialize mocks
@Mock
private EmployeeRepository repository; // Not initialized

@Test
public void test() {
    repository.findById(1); // NPE!
}

// Fix: Add initialization
@Before
public void setup() {
    MockitoAnnotations.initMocks(this);
}

// Cause 2: Mock returned null (not stubbed)
@Test
public void test() {
    Employee emp = repository.findById(1); // Returns null
    emp.getName(); // NPE!
}

// Fix: Stub the method
when(repository.findById(1)).thenReturn(employee);
```

---

### **162. What does "Wanted but not invoked" error mean?**

The method you tried to verify was never actually called.

**Example:**
```java
@Test
public void test() {
    service.createReport(1);
    
    verify(repository).save(any()); // Error: Wanted but not invoked!
}

// Reason: createReport doesn't call repository.save()
// Fix: Either fix the test or fix the code
```

---

### **163. What does "Argument mismatch" error mean?**

The method was called but with different arguments than expected.

**Example:**
```java
@Test
public void test() {
    service.deleteReport(5);
    
    verify(repository).deleteById(1); // Error: was called with 5, not 1!
}

// Fix: Use correct argument or argument matcher
verify(repository).deleteById(5); // Correct
// Or
verify(repository).deleteById(anyInt()); // Any integer
```

---

### **164. What does "Unfinished stubbing" error mean?**

You started stubbing but didn't complete it properly.

**Example:**
```java
@Test
public void test() {
    when(repository.findById(1)); // Incomplete! Missing thenReturn()
}

// Fix: Complete the stubbing
when(repository.findById(1)).thenReturn(employee);
```

---

### **165. What does "UnnecessaryStubbingException" mean?**

You stubbed a method that was never used in the test. Mockito's strict mode detects unused stubs.

**Example:**
```java
@Test
public void test() {
    when(repository.count()).thenReturn(10); // Stubbed
    when(repository.findAll()).thenReturn(employees); // Stubbed but NEVER used!
    
    int count = repository.count(); // Only count() is used
    
    // Error: findAll() was stubbed but not used
}

// Fix 1: Remove unused stubbing
// Fix 2: Use lenient stubbing
lenient().when(repository.findAll()).thenReturn(employees);
```

---

### **166. Why does my test pass when run alone but fail when run with other tests?**

Test pollution - tests are sharing state or affecting each other.

**Common causes:**
```java
// Cause: Static variables shared between tests
public class ServiceTest {
    static List<Employee> sharedList = new ArrayList<>();
    
    @Test
    public void test1() {
        sharedList.add(employee); // Modifies shared state
    }
    
    @Test
    public void test2() {
        assertEquals(0, sharedList.size()); // Fails if test1 ran first!
    }
}

// Fix: Reset state in @Before or avoid static variables
@Before
public void setup() {
    sharedList = new ArrayList<>();
}
```

---

### **167. Why does assertEquals fail for double values?**

Floating-point precision issues. Always use delta for doubles.

**Example:**
```java
@Test
public void test() {
    double result = 0.1 + 0.2;
    assertEquals(0.3, result); // Might fail! result is 0.30000000000000004
}

// Fix: Use delta
assertEquals(0.3, result, 0.0001); // Passes
```

---

### **168. What does "No tests found" error mean?**

JUnit can't find test methods in your test class.

**Common causes:**
```java
// Cause 1: Missing @Test annotation
public void testSomething() { // No @Test - not recognized!
}

// Cause 2: Wrong import
import org.junit.Test; // JUnit 4
// vs
import org.junit.jupiter.api.Test; // JUnit 5

// Cause 3: Non-public test method
@Test
private void test() { // Must be public!
}

// Cause 4: Test class not public
class ServiceTest { // Must be public!
}
```

---

### **169. Why does my test hang or timeout?**

Usually infinite loops or waiting for something that never completes.

**Example:**
```java
@Test(timeout = 1000)
public void test() {
    service.processAsync(); // Starts async task
    // Test ends immediately - async task still running!
}

// Fix: Wait for async completion
@Test
public void test() throws Exception {
    CompletableFuture<Report> future = service.processAsync();
    Report result = future.get(5, TimeUnit.SECONDS); // Wait properly
}
```

---

### **170. What does "cannot mock final class" mean?**

Older Mockito versions can't mock final classes.

**Example:**
```java
public final class FinalService { }

@Mock
private FinalService service; // Error in old Mockito!

// Fix 1: Use Mockito 2.0+ with mockito-inline
// Fix 2: Make class non-final if possible
// Fix 3: Use interface instead
```

---

## **PART 12: Test Organization and Best Practices**

### **171. How should you organize test classes?**

Mirror your source code structure. One test class per production class.

**Example:**
```
src/main/java/
  com/company/service/
    ReportService.java
    EmployeeService.java
  com/company/repository/
    ReportRepository.java

src/test/java/
  com/company/service/
    ReportServiceTest.java
    EmployeeServiceTest.java
  com/company/repository/
    ReportRepositoryTest.java
```

---

### **172. Should you use nested test classes?**

Yes, for grouping related tests. Use @Nested in JUnit 5.

**Example:**
```java
public class ReportServiceTest {
    
    @Nested
    class CreateReportTests {
        @Test
        public void testCreateWithValidData() { }
        
        @Test
        public void testCreateWithInvalidData() { }
    }
    
    @Nested
    class DeleteReportTests {
        @Test
        public void testDeleteExisting() { }
        
        @Test
        public void testDeleteNonExisting() { }
    }
}
```

---

### **173. How should you name test files?**

Use `ClassNameTest` pattern for unit tests.

**Examples:**
- `ReportService.java` → `ReportServiceTest.java`
- `EmployeeRepository.java` → `EmployeeRepositoryTest.java`

For integration tests, add suffix like `IntegrationTest` or `IT`:
- `ReportServiceIntegrationTest.java`
- `EmployeeControllerIT.java`

---

### **174. Where should test data creation methods go?**

Create test data builders or factory methods in the same test class or separate test utility class.

**Example:**
```java
public class ReportServiceTest {
    
    @Test
    public void test() {
        Employee emp = createTestEmployee();
        Report report = createTestReport(emp);
    }
    
    // Test data helpers
    private Employee createTestEmployee() {
        Employee emp = new Employee("John");
        emp.setDepartment("IT");
        emp.setSalary(50000);
        return emp;
    }
    
    private Report createTestReport(Employee emp) {
        Report report = new Report();
        report.setEmployee(emp);
        report.setDate(new Date());
        return report;
    }
}

// Or create separate utility class for complex data
public class TestDataBuilder {
    public static Employee createEmployee(String name) {
        // ...
    }
}
```

---

### **175. Should you use constants for test data?**

Use constants for values that appear multiple times or have business meaning.

**Example:**
```java
public class ReportServiceTest {
    private static final int EMPLOYEE_ID = 1;
    private static final String DEPARTMENT_IT = "IT";
    private static final double HIGH_PERFORMANCE_SCORE = 90.0;
    private static final double LOW_PERFORMANCE_SCORE = 60.0;
    
    @Test
    public void testHighPerformer() {
        Employee emp = createEmployee(HIGH_PERFORMANCE_SCORE);
        // Clear what's being tested
    }
}
```

---

### **176. How do you handle test data files?**

Put them in `src/test/resources` folder.

**Example:**
```
src/test/resources/
  test-data.sql
  sample-report.json
  test-config.properties
  
@Test
public void test() {
    InputStream stream = getClass()
        .getResourceAsStream("/sample-report.json");
    // Use the test data file
}
```

---

### **177. Should you commit test databases?**

Use in-memory databases for tests, not committed database files.

**Example:**
```yaml
# application-test.yml
spring:
  datasource:
    url: jdbc:h2:mem:testdb  # In-memory, fresh for each test run
    driver-class-name: org.h2.Driver
  jpa:
    hibernate:
      ddl-auto: create-drop  # Auto create/drop schema
```

---

### **178. How many assertions per test method?**

One logical concept per test, but multiple assertions are okay if they verify the same concept.

**Example - Good:**
```java
@Test
public void testEmployeeCreation() {
    Employee emp = service.createEmployee("John", "IT", 50000);
    
    // All verify the creation was successful
    assertNotNull(emp.getId());
    assertEquals("John", emp.getName());
    assertEquals("IT", emp.getDepartment());
    assertEquals(50000, emp.getSalary(), 0.01);
    assertTrue(emp.isActive());
}
```

**Example - Bad:**
```java
@Test
public void testEverything() {
    // Test creation
    Employee emp = service.createEmployee("John");
    assertNotNull(emp);
    
    // Test update (different concept - should be separate test!)
    service.updateSalary(emp, 60000);
    assertEquals(60000, emp.getSalary());
    
    // Test delete (another concept - separate test!)
    service.delete(emp);
    verify(repository).delete(emp);
}
```

---

### **179. Should test methods be in any specific order?**

Tests should be independent of order. Don't rely on execution sequence.

**Example - Bad:**
```java
@Test
public void test1_createEmployee() {
    savedEmployee = service.save(employee); // Step 1
}

@Test
public void test2_updateEmployee() {
    service.update(savedEmployee); // Depends on test1!
}
```

**Example - Good:**
```java
@Before
public void setup() {
    savedEmployee = service.save(employee);
}

@Test
public void testCreate() {
    // Independent
}

@Test
public void testUpdate() {
    service.update(savedEmployee); // Each test has fresh data from @Before
}
```

---

### **180. How do you document complex test scenarios?**

Use descriptive test names and add comments for non-obvious business rules.

**Example:**
```java
@Test
public void testPromotion_whenEmployeeHas5YearsTenureAnd85Score_shouldPromoteToSenior() {
    // Business rule: Employees with 5+ years AND 85+ score get promoted
    // This edge case tests exactly 5 years and exactly 85 score
    Employee emp = createEmployee(5, 85);
    
    boolean promoted = service.evaluateForPromotion(emp);
    
    assertTrue(promoted);
    assertEquals("Senior Developer", emp.getPosition());
}
```

---

I've now covered 180 questions comprehensively! 

**Should I continue with the final set covering:**
- Performance testing considerations
- Mocking frameworks comparison
- CI/CD integration
- Code coverage tools
- Real PerformanceTrack module specific questions

Type **"continue"** for the final comprehensive set!

# JUnit and Mockito Interview Questions - Final Part

## **PART 13: Performance Testing and Advanced Topics**

### **181. Should unit tests test performance?**

Generally no. Unit tests verify correctness, not performance. Create separate performance test suite if needed.

**Example:**
```java
// Unit test - tests correctness, not speed
@Test
public void testCalculateScore() {
    double score = service.calculateScore(80, 90);
    assertEquals(85.0, score, 0.01); // Correct result
}

// Performance test - separate suite
@Test
@Category(PerformanceTests.class)
public void testCalculateScorePerformance() {
    long start = System.currentTimeMillis();
    
    for (int i = 0; i < 10000; i++) {
        service.calculateScore(80, 90);
    }
    
    long duration = System.currentTimeMillis() - start;
    assertTrue("Too slow", duration < 1000); // Under 1 second for 10k iterations
}
```

---

### **182. How do you test that a method completes within a time limit?**

Use `@Test(timeout = milliseconds)` parameter.

**Example:**
```java
@Test(timeout = 500) // Must complete within 500ms
public void testReportGenerationSpeed() {
    service.generateReport(employeeId);
    // If takes more than 500ms, test fails
}

@Test(timeout = 2000)
public void testBulkProcessing() {
    service.processBulkEmployees(employees);
    // Must complete within 2 seconds
}
```

---

### **183. What is the difference between unit test and performance test?**

**Unit test** - Verifies code works correctly (functional correctness)
**Performance test** - Verifies code works fast enough (non-functional requirement)

**Example:**
```java
// Unit test - Is the calculation correct?
@Test
public void testBonus() {
    assertEquals(5000, service.calculateBonus(50000));
}

// Performance test - Can we calculate 100,000 bonuses quickly?
@Test
public void testBonusPerformance() {
    long start = System.currentTimeMillis();
    for (int i = 0; i < 100000; i++) {
        service.calculateBonus(50000);
    }
    long duration = System.currentTimeMillis() - start;
    assertTrue(duration < 1000); // Should handle 100k in 1 second
}
```

---

### **184. How do you test memory leaks in Java code?**

Unit tests don't typically test for memory leaks. Use profiling tools like JProfiler, VisualVM.

**Example - Basic memory check:**
```java
@Test
public void testNoMemoryLeak() {
    Runtime runtime = Runtime.getRuntime();
    runtime.gc(); // Suggest garbage collection
    long memoryBefore = runtime.totalMemory() - runtime.freeMemory();
    
    // Run operation many times
    for (int i = 0; i < 10000; i++) {
        service.processReport(i);
    }
    
    runtime.gc();
    long memoryAfter = runtime.totalMemory() - runtime.freeMemory();
    long memoryIncrease = memoryAfter - memoryBefore;
    
    // Memory shouldn't grow too much
    assertTrue("Possible memory leak", memoryIncrease < 10_000_000); // 10MB
}
```

This is crude - better to use profiling tools.

---

### **185. Can you run tests in parallel?**

Yes! Use Maven Surefire plugin or JUnit parallel execution.

**Example - Maven configuration:**
```xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-surefire-plugin</artifactId>
    <configuration>
        <parallel>methods</parallel>
        <threadCount>4</threadCount>
    </configuration>
</plugin>
```

**Important:** Tests must be completely independent for parallel execution!

---

### **186. What happens if tests modify shared static variables?**

Tests will interfere with each other, especially in parallel execution. Avoid static state.

**Example - Problem:**
```java
public class ServiceTest {
    static int counter = 0; // DANGEROUS!
    
    @Test
    public void test1() {
        counter++; // Modifies shared state
        assertEquals(1, counter); // Fails in parallel execution
    }
    
    @Test
    public void test2() {
        counter++;
        assertEquals(1, counter); // Fails in parallel execution
    }
}
```

**Fix:** Use instance variables and reset in @Before.

---

### **187. How do you measure test execution time?**

Most IDEs and Maven show execution time. Can also add logging.

**Example:**
```java
@Rule
public TestRule benchmark = new TestWatcher() {
    protected void finished(Description description) {
        System.out.println(description.getMethodName() + " completed");
    }
};

@Test
public void test() {
    // Test code
}
// Prints: "test completed"
```

Better: Use Maven Surefire reports or IDE built-in timers.

---

### **188. Should slow tests be separated from fast tests?**

Yes! Use test categories or separate test suites.

**Example:**
```java
// Marker interfaces
public interface FastTests { }
public interface SlowTests { }

// Categorize tests
@Category(FastTests.class)
@Test
public void testQuickCalculation() { }

@Category(SlowTests.class)
@Test
public void testDatabaseIntegration() { }

// Run only fast tests in development
@RunWith(Categories.class)
@Categories.IncludeCategory(FastTests.class)
@Suite.SuiteClasses({AllTests.class})
public class FastTestSuite { }
```

---

### **189. What is test isolation and why is it important?**

Each test should run independently without affecting or depending on other tests.

**Example of good isolation:**
```java
@Before
public void setupEachTest() {
    employee = new Employee("John"); // Fresh data for each test
    MockitoAnnotations.initMocks(this); // Fresh mocks
}

@Test
public void test1() {
    employee.setSalary(50000);
    // Test doesn't affect test2
}

@Test
public void test2() {
    employee.setSalary(60000);
    // Gets fresh employee from @Before
}
```

Benefits: Tests can run in any order, can run in parallel, easier debugging.

---

### **190. How do you test multi-threaded code?**

Use CountDownLatch, CyclicBarrier, or Awaitility library.

**Example:**
```java
@Test
public void testConcurrentAccess() throws InterruptedException {
    int threadCount = 10;
    CountDownLatch startLatch = new CountDownLatch(1);
    CountDownLatch doneLatch = new CountDownLatch(threadCount);
    
    for (int i = 0; i < threadCount; i++) {
        new Thread(() -> {
            try {
                startLatch.await(); // Wait for signal to start
                service.incrementCounter();
            } catch (Exception e) {
                e.printStackTrace();
            } finally {
                doneLatch.countDown();
            }
        }).start();
    }
    
    startLatch.countDown(); // Signal all threads to start
    doneLatch.await(5, TimeUnit.SECONDS); // Wait for completion
    
    assertEquals(10, service.getCounter()); // All increments happened
}
```

---

## **PART 14: Code Coverage and Quality**

### **191. What is code coverage?**

Code coverage measures what percentage of your code is executed during tests.

**Types:**
- **Line coverage** - % of code lines executed
- **Branch coverage** - % of if/else branches tested
- **Method coverage** - % of methods called

**Example:**
```java
public int calculate(int score) {
    if (score > 80) {        // Line 1
        return score * 2;     // Line 2
    } else {                 // Line 3
        return score;         // Line 4
    }
}

@Test
public void testHighScore() {
    assertEquals(180, calculate(90)); // Covers lines 1, 2
    // Line coverage: 50% (only 2 out of 4 lines)
    // Branch coverage: 50% (only if branch, not else)
}
```

---

### **192. What tools measure code coverage in Java?**

Common tools:
- **JaCoCo** - Most popular, integrates with Maven/Gradle
- **Cobertura** - Older, still used
- **Emma** - Lightweight
- IDE built-in (IntelliJ, Eclipse)

**Example - JaCoCo with Maven:**
```xml
<plugin>
    <groupId>org.jacoco</groupId>
    <artifactId>jacoco-maven-plugin</artifactId>
    <version>0.8.7</version>
    <executions>
        <execution>
            <goals>
                <goal>prepare-agent</goal>
                <goal>report</goal>
            </goals>
        </execution>
    </executions>
</plugin>
```

Run: `mvn clean test jacoco:report`

---

### **193. What is a good code coverage percentage target?**

**Industry standards:**
- 70-80% is good for most projects
- 80-90% is very good
- Critical systems might aim for 90%+
- 100% is rarely necessary or achievable

**Example for PerformanceTrack:**
```
Analytics Module:
- ReportService: 85% (critical business logic)
- ReportController: 75% (REST endpoints)
- ReportRepository: 60% (simple CRUD)
- Utils: 90% (heavily used helpers)

Overall target: 75-80%
```

Focus on high-value code, not just hitting a number.

---

### **194. Does 100% code coverage mean no bugs?**

No! Coverage shows what's tested, not if tests are good.

**Example - 100% coverage but bad test:**
```java
public int divide(int a, int b) {
    return a / b; // Bug: no check for b == 0
}

@Test
public void testDivide() {
    divide(10, 2); // 100% coverage!
    // But doesn't test divide-by-zero case
}
```

Coverage is necessary but not sufficient for quality.

---

### **195. What is branch coverage vs line coverage?**

**Line coverage** - Did we execute this line?
**Branch coverage** - Did we test both true and false of each condition?

**Example:**
```java
public String getGrade(int score) {
    if (score >= 90) {
        return "A";
    } else if (score >= 80) {
        return "B";
    } else {
        return "C";
    }
}

@Test
public void testGradeA() {
    assertEquals("A", getGrade(95));
    // Line coverage: 3/5 = 60%
    // Branch coverage: 1/3 = 33% (only first if tested)
}

// Need more tests for full branch coverage
@Test public void testGradeB() { assertEquals("B", getGrade(85)); }
@Test public void testGradeC() { assertEquals("C", getGrade(70)); }
// Now: Line coverage 100%, Branch coverage 100%
```

---

### **196. Should you test code that just calls other code?**

Depends. If it's pure delegation with no logic, maybe skip. If it orchestrates multiple calls, test it.

**Example - Skip testing:**
```java
public void saveEmployee(Employee emp) {
    repository.save(emp); // Just delegation, no logic
}
```

**Example - Should test:**
```java
public void saveEmployee(Employee emp) {
    validate(emp);           // Logic 1
    repository.save(emp);    // Call 2
    auditLogger.log(emp);    // Call 3
    emailService.notify(emp); // Call 4
    // Orchestration logic - test this!
}
```

---

### **197. How do you ignore code from coverage reports?**

Use tool-specific annotations or comments.

**Example - JaCoCo:**
```java
public class Utils {
    
    // Main method - exclude from coverage
    public static void main(String[] args) {
        // Generated code, not business logic
    }
    
    // Auto-generated equals/hashCode
    @Override
    public boolean equals(Object obj) {
        // Can exclude if generated
    }
}
```

In `jacoco.xml`:
```xml
<configuration>
    <excludes>
        <exclude>**/*Application.class</exclude>
        <exclude>**/config/**</exclude>
    </excludes>
</configuration>
```

---

### **198. What is mutation testing?**

Mutation testing changes (mutates) your code and checks if tests catch the changes.

**Example:**
```java
// Original code
public boolean isEligible(int score) {
    return score >= 80;
}

@Test
public void test() {
    assertTrue(isEligible(85));
}

// Mutation 1: Change >= to >
public boolean isEligible(int score) {
    return score > 80; // Mutated
}
// Does test catch this? NO! Test still passes.
// This reveals weak test.

// Better test needed:
@Test
public void testBoundary() {
    assertTrue(isEligible(80)); // Now catches the >= vs > mutation
    assertFalse(isEligible(79));
}
```

Tools: PIT (Pitest) for Java

---

### **199. What is the test pyramid concept?**

A testing strategy with three layers:

**Bottom (70%)** - Unit tests: Fast, many, isolated
**Middle (20%)** - Integration tests: Medium speed, fewer
**Top (10%)** - End-to-end tests: Slow, very few

**Example for PerformanceTrack:**
```
E2E Tests (10%): 
- Complete user flow: Login → View Dashboard → Generate Report

Integration Tests (20%):
- ReportController + ReportService + Database
- Authentication flow with real database

Unit Tests (70%):
- ReportService.calculateScore()
- ReportService.generateReport()
- ReportController endpoints (mocked service)
- All utility methods
```

More unit tests because they're fast and reliable.

---

### **200. Should you test generated code (like Lombok)?**

No. Don't test auto-generated getters/setters or Lombok-generated code.

**Example - Don't test:**
```java
@Data // Lombok generates getters/setters/equals/hashCode
public class Employee {
    private String name;
    private int salary;
}

// DON'T write these tests:
@Test
public void testGetName() {
    employee.setName("John");
    assertEquals("John", employee.getName());
}
```

Trust the library. Focus on your business logic.

---

## **PART 15: PerformanceTrack Specific Questions**

### **201. How would you test the Analytics module's calculateAverageScore method?**

Test with various employee performance scenarios.

**Example:**
```java
@Mock
private GoalRepository goalRepository;
@Mock
private ReviewRepository reviewRepository;

@InjectMocks
private ReportService reportService;

@Test
public void testCalculateAverageScore_WithGoalsAndReviews() {
    // Arrange
    List<Goal> goals = Arrays.asList(
        createGoal(85, true),
        createGoal(90, true),
        createGoal(75, false) // Not completed - should ignore
    );
    
    List<Review> reviews = Arrays.asList(
        createReview(88),
        createReview(92)
    );
    
    when(goalRepository.findByEmployeeId(1)).thenReturn(goals);
    when(reviewRepository.findByEmployeeId(1)).thenReturn(reviews);
    
    // Act
    double avgScore = reportService.calculateAverageScore(1);
    
    // Assert
    // Average of completed goals (85, 90) = 87.5
    // Average of reviews (88, 92) = 90
    // Overall average = (87.5 + 90) / 2 = 88.75
    assertEquals(88.75, avgScore, 0.01);
}

@Test
public void testCalculateAverageScore_NoCompletedGoals() {
    List<Goal> goals = Arrays.asList(
        createGoal(85, false),
        createGoal(90, false)
    );
    List<Review> reviews = Arrays.asList(createReview(88));
    
    when(goalRepository.findByEmployeeId(1)).thenReturn(goals);
    when(reviewRepository.findByEmployeeId(1)).thenReturn(reviews);
    
    double avgScore = reportService.calculateAverageScore(1);
    
    // Only review score counts
    assertEquals(88.0, avgScore, 0.01);
}

@Test
public void testCalculateAverageScore_NoData() {
    when(goalRepository.findByEmployeeId(1)).thenReturn(new ArrayList<>());
    when(reviewRepository.findByEmployeeId(1)).thenReturn(new ArrayList<>());
    
    double avgScore = reportService.calculateAverageScore(1);
    
    assertEquals(0.0, avgScore, 0.01);
}
```

---

### **202. How would you test the Analytics module's report generation with JWT authentication?**

Mock the authentication and test the controller.

**Example:**
```java
@RunWith(SpringRunner.class)
@WebMvcTest(ReportController.class)
public class ReportControllerTest {
    
    @Autowired
    private MockMvc mockMvc;
    
    @MockBean
    private ReportService reportService;
    
    @MockBean
    private JwtTokenProvider tokenProvider;
    
    @Test
    public void testGenerateReport_WithValidToken() throws Exception {
        Report report = new Report();
        report.setId(1);
        report.setEmployeeName("John");
        
        when(tokenProvider.validateToken(anyString())).thenReturn(true);
        when(tokenProvider.getUserIdFromToken(anyString())).thenReturn("1");
        when(reportService.generateReport(1)).thenReturn(report);
        
        mockMvc.perform(get("/api/reports/1")
                .header("Authorization", "Bearer valid-token"))
            .andExpect(status().isOk())
            .andExpect(jsonPath("$.id").value(1))
            .andExpect(jsonPath("$.employeeName").value("John"));
        
        verify(reportService).generateReport(1);
    }
    
    @Test
    public void testGenerateReport_WithInvalidToken() throws Exception {
        when(tokenProvider.validateToken(anyString())).thenReturn(false);
        
        mockMvc.perform(get("/api/reports/1")
                .header("Authorization", "Bearer invalid-token"))
            .andExpect(status().isUnauthorized());
        
        verify(reportService, never()).generateReport(any());
    }
    
    @Test
    public void testGenerateReport_WithoutToken() throws Exception {
        mockMvc.perform(get("/api/reports/1"))
            .andExpect(status().isUnauthorized());
    }
}
```

---

### **203. How would you test that manager approval is required for goal submission?**

Test the approval workflow logic.

**Example:**
```java
@Test
public void testGoalSubmission_RequiresManagerApproval() {
    Goal goal = new Goal("Complete project");
    goal.setEmployeeId(1);
    goal.setStatus("PENDING");
    
    when(employeeRepository.findById(1)).thenReturn(employee);
    when(goalRepository.save(any())).thenReturn(goal);
    
    Goal submitted = goalService.submitGoal(goal);
    
    assertEquals("PENDING_APPROVAL", submitted.getStatus());
    assertFalse(submitted.isApproved());
    verify(notificationService).notifyManager(employee.getManagerId(), goal);
}

@Test
public void testGoalApproval_OnlyManagerCanApprove() {
    Goal goal = new Goal();
    goal.setEmployeeId(1);
    goal.setStatus("PENDING_APPROVAL");
    
    Employee employee = new Employee();
    employee.setManagerId(5);
    
    when(goalRepository.findById(1)).thenReturn(goal);
    when(employeeRepository.findById(1)).thenReturn(employee);
    
    // Manager (ID 5) approving - should work
    goalService.approveGoal(1, 5);
    
    verify(goalRepository).save(argThat(g -> 
        g.getStatus().equals("APPROVED") && g.isApproved()
    ));
}

@Test(expected = UnauthorizedException.class)
public void testGoalApproval_NonManagerCannotApprove() {
    Goal goal = new Goal();
    goal.setEmployeeId(1);
    
    Employee employee = new Employee();
    employee.setManagerId(5);
    
    when(goalRepository.findById(1)).thenReturn(goal);
    when(employeeRepository.findById(1)).thenReturn(employee);
    
    // Non-manager (ID 10) trying to approve - should fail
    goalService.approveGoal(1, 10);
}
```

---

### **204. How would you test the feedback module integration with Analytics?**

Test that feedback data is correctly read and aggregated.

**Example:**
```java
@Test
public void testAnalyticsIncludesFeedbackData() {
    Employee employee = new Employee("John");
    
    List<Feedback> feedbacks = Arrays.asList(
        createFeedback("Good work", 90),
        createFeedback("Needs improvement", 70),
        createFeedback("Excellent", 95)
    );
    
    when(employeeRepository.findById(1)).thenReturn(employee);
    when(feedbackRepository.findByEmployeeId(1)).thenReturn(feedbacks);
    
    AnalyticsReport analytics = reportService.generateAnalytics(1);
    
    assertEquals(85.0, analytics.getAverageFeedbackScore(), 0.01); // (90+70+95)/3
    assertEquals(3, analytics.getTotalFeedbackCount());
    assertTrue(analytics.getFeedbackTrend().equals("POSITIVE"));
}

@Test
public void testAnalytics_WithNoFeedback() {
    when(employeeRepository.findById(1)).thenReturn(employee);
    when(feedbackRepository.findByEmployeeId(1)).thenReturn(new ArrayList<>());
    
    AnalyticsReport analytics = reportService.generateAnalytics(1);
    
    assertEquals(0.0, analytics.getAverageFeedbackScore(), 0.01);
    assertEquals(0, analytics.getTotalFeedbackCount());
}
```

---

### **205. How would you test audit logging in your Analytics module?**

Verify that all important operations are logged.

**Example:**
```java
@Mock
private AuditLogger auditLogger;

@Test
public void testReportGeneration_LogsAudit() {
    Employee employee = new Employee("John");
    when(employeeRepository.findById(1)).thenReturn(employee);
    
    Report report = reportService.generateReport(1);
    
    verify(auditLogger).log(
        eq("REPORT_GENERATED"),
        eq(1),
        contains("John"),
        any(Date.class)
    );
}

@Test
public void testReportDeletion_LogsAudit() {
    reportService.deleteReport(1);
    
    verify(auditLogger).log(
        eq("REPORT_DELETED"),
        eq(1),
        anyString(),
        any(Date.class)
    );
}

@Test
public void testReportAccess_LogsAudit() {
    when(reportRepository.findById(1)).thenReturn(report);
    
    reportService.getReport(1, "user@company.com");
    
    verify(auditLogger).log(
        eq("REPORT_ACCESSED"),
        eq(1),
        eq("user@company.com"),
        any(Date.class)
    );
}
```

---

### **206. How would you test that only authorized users can access reports?**

Test role-based access control.

**Example:**
```java
@Test
@WithMockUser(username = "john", roles = "EMPLOYEE")
public void testEmployee_CanAccessOwnReport() throws Exception {
    Report report = new Report();
    report.setEmployeeId(1);
    
    when(reportService.getReport(1)).thenReturn(report);
    when(userService.getCurrentUserId()).thenReturn(1);
    
    mockMvc.perform(get("/api/reports/1"))
        .andExpect(status().isOk());
}

@Test
@WithMockUser(username = "alice", roles = "EMPLOYEE")
public void testEmployee_CannotAccessOthersReport() throws Exception {
    when(userService.getCurrentUserId()).thenReturn(2);
    
    mockMvc.perform(get("/api/reports/1"))
        .andExpect(status().isForbidden());
}

@Test
@WithMockUser(username = "manager", roles = "MANAGER")
public void testManager_CanAccessTeamReports() throws Exception {
    Report report = new Report();
    when(reportService.getReport(1)).thenReturn(report);
    when(userService.isManagerOf(anyInt())).thenReturn(true);
    
    mockMvc.perform(get("/api/reports/1"))
        .andExpect(status().isOk());
}

@Test
@WithMockUser(username = "admin", roles = "ADMIN")
public void testAdmin_CanAccessAllReports() throws Exception {
    mockMvc.perform(get("/api/reports/1"))
        .andExpect(status().isOk());
}
```

---

### **207. How would you test the complete workflow from goal creation to analytics?**

Write an integration test covering the entire flow.

**Example:**
```java
@RunWith(SpringRunner.class)
@SpringBootTest
@Transactional
public class PerformanceWorkflowIntegrationTest {
    
    @Autowired
    private GoalService goalService;
    
    @Autowired
    private ReviewService reviewService;
    
    @Autowired
    private ReportService reportService;
    
    @Autowired
    private EmployeeRepository employeeRepository;
    
    @Test
    public void testCompletePerformanceWorkflow() {
        // 1. Create employee
        Employee emp = new Employee("John", "john@company.com");
        emp = employeeRepository.save(emp);
        
        // 2. Create goals
        Goal goal1 = new Goal("Complete Project A");
        goal1.setEmployeeId(emp.getId());
        goal1 = goalService.create(goal1);
        
        // 3. Approve goals (as manager)
        goalService.approveGoal(goal1.getId(), emp.getManagerId());
        
        // 4. Complete goals
        goalService.updateProgress(goal1.getId(), 100);
        goalService.complete(goal1.getId(), 90); // Score 90
        
        // 5. Create review
        Review review = new Review();
        review.setEmployeeId(emp.getId());
        review.setScore(85);
        review = reviewService.create(review);
        
        // 6. Generate analytics report
        Report analyticsReport = reportService.generateAnalytics(emp.getId());
        
        // 7. Verify complete workflow
        assertNotNull(analyticsReport);
        assertEquals("John", analyticsReport.getEmployeeName());
        assertTrue(analyticsReport.getAverageScore() > 0);
        assertEquals(1, analyticsReport.getCompletedGoalsCount());
        assertEquals(1, analyticsReport.getReviewsCount());
        
        // Average of goal (90) and review (85) = 87.5
        assertEquals(87.5, analyticsReport.getAverageScore(), 0.01);
    }
}
```

---

### **208. How would you test error handling when database connection fails?**

Mock repository to throw exceptions.

**Example:**
```java
@Test
public void testReportGeneration_HandlesDbError() {
    when(goalRepository.findByEmployeeId(1))
        .thenThrow(new DataAccessException("Database connection failed"));
    
    try {
        reportService.generateReport(1);
        fail("Should have thrown exception");
    } catch (ServiceException e) {
        assertEquals("Unable to generate report", e.getMessage());
        verify(auditLogger).logError(contains("Database connection failed"));
    }
}

@Test
public void testReportGeneration_RetriesOnTransientError() {
    // First two calls fail, third succeeds
    when(goalRepository.findByEmployeeId(1))
        .thenThrow(new TransientDataAccessException())
        .thenThrow(new TransientDataAccessException())
        .thenReturn(goals);
    
    Report report = reportService.generateReportWithRetry(1);
    
    assertNotNull(report);
    verify(goalRepository, times(3)).findByEmployeeId(1);
    verify(auditLogger).log(contains("Retried 2 times"));
}
```

---

### **209. How would you test pagination in Analytics reports listing?**

Test different page scenarios.

**Example:**
```java
@Test
public void testGetReports_FirstPage() {
    List<Report> allReports = createReports(25);
    when(reportRepository.findAll(any(Pageable.class)))
        .thenReturn(new PageImpl<>(allReports.subList(0, 10)));
    
    Page<Report> page = reportService.getReports(0, 10);
    
    assertEquals(10, page.getContent().size());
    assertEquals(0, page.getNumber());
    assertTrue(page.hasNext());
    assertFalse(page.hasPrevious());
}

@Test
public void testGetReports_LastPage() {
    List<Report> allReports = createReports(25);
    when(reportRepository.findAll(any(Pageable.class)))
        .thenReturn(new PageImpl<>(allReports.subList(20, 25)));
    
    Page<Report> page = reportService.getReports(2, 10);
    
    assertEquals(5, page.getContent().size()); // Only 5 items
    assertEquals(2, page.getNumber());
    assertFalse(page.hasNext());
    assertTrue(page.hasPrevious());
}

private List<Report> createReports(int count) {
    List<Report> reports = new ArrayList<>();
    for (int i = 1; i <= count; i++) {
        Report r = new Report();
        r.setId(i);
        reports.add(r);
    }
    return reports;
}
```

---

### **210. How would you prepare for interview questions about your Analytics module?**

**Be ready to explain:**

1. **Your module's responsibility:**
   "The Analytics module reads data from Goals, Reviews, and Feedback modules to calculate performance scores and generate reports."

2. **Key methods you wrote:**
   "I implemented calculateAverageScore() which aggregates goal completion rates and review scores."

3. **How you tested it:**
   "I wrote unit tests mocking the repositories, integration tests with H2 database, and tested edge cases like no data or partial data."

4. **Challenges faced:**
   "Handling cases where employees have goals but no reviews, or vice versa. I had to decide whether to show zero or skip them."

5. **Testing approach:**
```java
// Example to mention in interview
@Test
public void testMyKeyMethod() {
    // Arrange - setup test data with mocks
    when(goalRepo.findByEmployeeId(1)).thenReturn(goals);
    
    // Act - call the method
    double score = service.calculateScore(1);
    
    // Assert - verify results
    assertEquals(85.0, score, 0.01);
    verify(goalRepo).findByEmployeeId(1);
}
```

---

## **Final Summary - Quick Reference**

### **Top 10 Most Asked Questions:**

1. **What is JUnit?** - Testing framework for Java
2. **What is Mockito?** - Framework for creating mock objects
3. **Difference between @Mock and @InjectMocks?** - @Mock creates mock, @InjectMocks injects mocks
4. **How to verify method was called?** - Use verify()
5. **How to stub a method?** - Use when().thenReturn()
6. **What is @Before and @After?** - Setup and cleanup for each test
7. **How to test exceptions?** - @Test(expected = Exception.class)
8. **Difference between unit and integration test?** - Unit tests one piece, integration tests multiple pieces
9. **What is code coverage?** - Percentage of code executed by tests
10. **How to test Spring Boot controllers?** - Use @WebMvcTest and MockMvc

### **Interview Preparation Checklist:**

✅ Understand JUnit lifecycle (@Before, @Test, @After)
✅ Know when to use mock vs spy
✅ Practice writing assertions
✅ Understand argument matchers (any(), eq(), etc.)
✅ Know how to verify interactions
✅ Understand Spring Boot test annotations
✅ Can explain your test strategy for PerformanceTrack
✅ Know how to test REST APIs with MockMvc
✅ Understand code coverage concepts
✅ Can write tests on whiteboard/paper

**Good luck with your interview! You've got this! 🎯**