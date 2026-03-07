# JDBC, Hibernate & Spring JPA Interview Questions

I'll give you comprehensive interview questions with conversational answers. Let me start with the first set:

## **JDBC Questions**

### 1. What is JDBC and why do we need it?

JDBC stands for Java Database Connectivity. Think of it as a bridge between your Java application and databases like MySQL, Oracle, or PostgreSQL.

**Why we need it:** Imagine you're building PerformanceTrack and need to store employee data. Without JDBC, you'd need different code for each database. JDBC provides a standard way to connect to any database using the same Java code.

**Example:** In your Analytics module, when you fetch performance review data, JDBC helps you connect to the database and retrieve that information.

### 2. What are the main components of JDBC?

JDBC has four key components:

**DriverManager** - Like a phone directory that knows which driver to use for which database.

**Driver** - The actual translator that speaks the database's language. For MySQL, you use MySQL driver; for Oracle, Oracle driver.

**Connection** - Once DriverManager finds the right driver, it creates a connection to your database, like opening a phone line.

**Statement** - This is what carries your SQL queries to the database.

**Example:** When your ReportService needs to calculate average performance scores, it uses all these components to fetch the data.

### 3. What's the difference between Statement, PreparedStatement, and CallableStatement?

**Statement** - Used for simple SQL queries that don't change. Like asking "Show me all employees" every time.

**PreparedStatement** - Used when you run the same query multiple times with different values. It's precompiled, so it's faster. Like a template: "Show me employee with ID = ?" where you fill in different IDs.

**CallableStatement** - Used to call stored procedures in the database.

**Example:** In PerformanceTrack, if you're searching for reviews by different employee IDs, use PreparedStatement. If you just want to count total reviews once, Statement works fine.

### 4. Why is PreparedStatement better than Statement?

Two main reasons:

**Performance** - PreparedStatement is compiled once and reused. If you're searching 100 employees, Statement compiles 100 times, but PreparedStatement compiles just once.

**Security** - PreparedStatement prevents SQL Injection attacks. 

**Example:** Imagine a hacker types in the search box: `105 OR 1=1`. With Statement, this could expose all data. PreparedStatement treats it as just a number, keeping your PerformanceTrack data safe.

### 5. What is SQL Injection and how does PreparedStatement prevent it?

SQL Injection is when hackers insert malicious SQL code through input fields.

**Bad example with Statement:**
```java
String query = "SELECT * FROM employees WHERE id = " + userId;
// If userId = "5 OR 1=1", it returns ALL employees!
```

**Safe with PreparedStatement:**
```java
String query = "SELECT * FROM employees WHERE id = ?";
PreparedStatement ps = conn.prepareStatement(query);
ps.setInt(1, userId); // Treats "5 OR 1=1" as invalid number, not SQL code
```

### 6. What are the steps to connect to a database using JDBC?

Five simple steps:

**Step 1:** Load the driver - `Class.forName("com.mysql.jdbc.Driver")`

**Step 2:** Create connection - `Connection conn = DriverManager.getConnection(url, username, password)`

**Step 3:** Create statement - `PreparedStatement ps = conn.prepareStatement(query)`

**Step 4:** Execute query - `ResultSet rs = ps.executeQuery()`

**Step 5:** Close resources - `rs.close(), ps.close(), conn.close()`

**Example:** When your Analytics module starts, it follows these steps to connect to the PerformanceTrack database.

### 7. What is a ResultSet and what are its types?

ResultSet is like a table that holds the query results. You can think of it as a cursor that points to rows of data.

**Three types:**

**TYPE_FORWARD_ONLY** - You can only move forward, row by row. Like reading a book page by page, can't go back.

**TYPE_SCROLL_INSENSITIVE** - You can move forward and backward, but won't see changes others make to the database.

**TYPE_SCROLL_SENSITIVE** - You can move both ways AND see live changes from other users.

**Example:** In your reporting dashboard, if you're just displaying a list, FORWARD_ONLY works. If users need to jump to page 5 then back to page 2, use SCROLL_INSENSITIVE.

### 8. What's the difference between executeQuery(), executeUpdate(), and execute()?

**executeQuery()** - Used for SELECT statements. Returns a ResultSet with data.
```java
ResultSet rs = ps.executeQuery("SELECT * FROM reviews");
```

**executeUpdate()** - Used for INSERT, UPDATE, DELETE. Returns number of rows affected.
```java
int rows = ps.executeUpdate("UPDATE goals SET status='Completed'");
```

**execute()** - Generic method that can run any SQL. Returns boolean (true if ResultSet, false if update count).

**Example:** When generating reports, you use executeQuery(). When a manager approves a goal, you use executeUpdate().

### 9. What is Connection Pooling and why is it important?

Creating a database connection is expensive - like building a phone line every time you make a call. Connection Pooling creates a pool of ready-to-use connections.

**How it works:** Instead of creating new connections, your app borrows from the pool, uses it, and returns it.

**Benefits:**
- Faster performance
- Handles more users simultaneously
- Less database server load

**Example:** In PerformanceTrack, when 50 managers log in simultaneously to review goals, connection pooling ensures everyone gets quick database access without overwhelming the server.

### 10. What are JDBC Transaction management methods?

Transactions ensure multiple database operations either all succeed or all fail together.

**Key methods:**

**setAutoCommit(false)** - Starts manual transaction control
**commit()** - Saves all changes permanently
**rollback()** - Undoes all changes if something fails

**Example:** When submitting a performance review in PerformanceTrack:
```java
conn.setAutoCommit(false);
// 1. Insert review record
// 2. Update employee's average score
// 3. Send notification
conn.commit(); // All succeed together

// If step 2 fails:
conn.rollback(); // Undo everything, keep data consistent
```

### 11. What is a JDBC Driver and what are its types?

JDBC Driver is the software that actually connects Java to the database. Think of it as a translator.

**Four types:**

**Type 1 - JDBC-ODBC Bridge** - Uses Windows ODBC drivers. Slow and platform-dependent. Rarely used now.

**Type 2 - Native-API Driver** - Uses database's native code. Faster but still platform-dependent.

**Type 3 - Network Protocol Driver** - Pure Java, connects through a middle server. Good for web apps.

**Type 4 - Thin Driver** - Pure Java, directly connects to database. Fastest and most popular.

**Example:** For PerformanceTrack, you'd use Type 4 (MySQL Connector/J) because it's pure Java, works everywhere, and is fastest.

### 12. What is the difference between getConnection() and DataSource?

**getConnection()** - Every time you call it, creates a new connection. Like rebuilding a bridge for every car.

**DataSource** - A factory that manages connections efficiently with pooling. Like having multiple lanes on a permanent bridge.

**Example:**
```java
// Old way - inefficient
Connection conn = DriverManager.getConnection(url, user, pass);

// Modern way - with connection pooling
DataSource ds = // configured data source
Connection conn = ds.getConnection(); // Gets from pool
```

In PerformanceTrack, using DataSource means 100 concurrent users don't create 100 new connections - they share from a pool of 10-20.

### 13. What is batch processing in JDBC?

Batch processing sends multiple SQL statements to the database in one go, instead of one at a time.

**Normal way:** Send 100 insert statements = 100 network trips
**Batch way:** Send all 100 together = 1 network trip

**Example:**
```java
PreparedStatement ps = conn.prepareStatement("INSERT INTO goals VALUES(?, ?)");
for(Goal goal : employeeGoals) {
    ps.setInt(1, goal.getId());
    ps.setString(2, goal.getTitle());
    ps.addBatch(); // Add to batch
}
ps.executeBatch(); // Send all at once
```

**Use case:** When importing 500 employee goals from Excel to PerformanceTrack, batch processing makes it 10x faster.

### 14. What is RowSet and how is it different from ResultSet?

RowSet is like an enhanced ResultSet that's more flexible.

**ResultSet** - Needs active database connection. When connection closes, ResultSet is gone.

**RowSet** - Can work without connection (disconnected). Can be serialized and sent over network.

**Two types:**
- **Connected** - JdbcRowSet (like ResultSet)
- **Disconnected** - CachedRowSet (works offline)

**Example:** In PerformanceTrack, if you want to export review data to Excel, you can use CachedRowSet. It loads data, closes connection, then processes data - saving database resources.

### 15. What are the different types of ResultSet concurrency?

**CONCUR_READ_ONLY** - You can only read data, not modify it through ResultSet.

**CONCUR_UPDATABLE** - You can update database directly through ResultSet without writing UPDATE queries.

**Example:**
```java
// Read-only
ResultSet rs = ps.executeQuery(); // Can only read

// Updatable
Statement st = conn.createStatement(
    ResultSet.TYPE_SCROLL_INSENSITIVE, 
    ResultSet.CONCUR_UPDATABLE
);
ResultSet rs = st.executeQuery("SELECT * FROM goals");
rs.next();
rs.updateString("status", "In Progress");
rs.updateRow(); // Updates database directly
```

In PerformanceTrack, managers might use updatable ResultSet to quickly update multiple goal statuses while browsing.

---

## **Hibernate Questions**

### 16. What is Hibernate and why do we use it instead of JDBC?

Hibernate is an ORM (Object-Relational Mapping) framework. It maps your Java objects to database tables automatically.

**JDBC problems Hibernate solves:**

**Too much code:** JDBC needs tons of boilerplate code for simple operations.

**Database dependent:** Changing from MySQL to Oracle means rewriting queries.

**No object handling:** JDBC works with ResultSets, not Java objects.

**Example:** In PerformanceTrack:

**With JDBC:**
```java
// 50 lines of code to save an employee
ps.setString(1, emp.getName());
ps.setString(2, emp.getEmail());
// ... many more lines
```

**With Hibernate:**
```java
session.save(employee); // Just one line!
```

### 17. What is ORM and how does Hibernate implement it?

ORM means Object-Relational Mapping - converting Java objects to database tables and vice versa.

**How it works:**

Your Java class `Employee` ↔ Database table `employees`
Object fields ↔ Table columns
Object instance ↔ Table row

**Hibernate does this automatically:**
- Generates SQL queries
- Maps results to objects
- Handles relationships between objects

**Example:** Your `Report.java` class in Analytics module is mapped to `reports` table. When you write `session.save(report)`, Hibernate converts it to INSERT SQL automatically.

### 18. What is the difference between JPA and Hibernate?

**JPA (Java Persistence API)** - It's a specification, like a blueprint or rulebook. It defines what ORM frameworks should do.

**Hibernate** - It's an implementation of JPA. It's the actual tool that follows JPA rules.

**Analogy:** JPA is like traffic rules, Hibernate is like a car that follows those rules.

**Example:** In PerformanceTrack, you write code using JPA annotations like `@Entity`, `@Id`. Hibernate runs behind the scenes to make it work. Tomorrow, you could switch to EclipseLink (another JPA implementation) without changing much code.

### 19. What are the different states of an object in Hibernate?

Every Hibernate object goes through different states in its lifecycle:

**Transient** - Just created with `new`, Hibernate doesn't know about it yet. Not in database.

**Persistent** - Hibernate is tracking it. Any changes auto-sync with database.

**Detached** - Was persistent, but session closed. Changes won't auto-sync.

**Example:**
```java
// Transient
Review review = new Review();

// Persistent - Hibernate tracks it
session.save(review);
review.setScore(85); // Auto-updates database

// Detached - session closed
session.close();
review.setScore(90); // Won't update database
```

In PerformanceTrack, when a manager opens a review form, loads data (persistent), closes form (detached), then reopens to edit (reattach to make persistent again).

### 20. What is Session in Hibernate?

Session is like a conversation with the database. It's the main interface to perform all database operations.

**Think of it as:** Your personal assistant that handles all database work for you.

**What it does:**
- Save, update, delete objects
- Run queries
- Manage transactions
- Cache objects for performance

**Example:**
```java
Session session = sessionFactory.openSession();
session.beginTransaction();

// Save a goal
session.save(goal);

// Load an employee
Employee emp = session.get(Employee.class, 101);

session.getTransaction().commit();
session.close();
```

In your Analytics module, you open a session when generating reports, use it to fetch data, then close it when done.

### 21. What's the difference between Session and SessionFactory?

**SessionFactory** - Heavy object created once for entire application. It's a factory that creates Sessions.

**Session** - Lightweight object created for each request/operation. One per user operation.

**Analogy:** SessionFactory is like a car factory (built once). Session is like individual cars (created as needed).

**Example:**
```java
// Created once when app starts
SessionFactory factory = new Configuration()
    .configure()
    .buildSessionFactory();

// Created many times - once per user request
Session session1 = factory.openSession(); // For user 1
Session session2 = factory.openSession(); // For user 2
```

In PerformanceTrack, SessionFactory is created when server starts. Every time a manager views reports, a new Session is created.

### 22. What is the difference between get() and load() methods?

Both retrieve objects from database, but differently:

**get()** - Hits database immediately. Returns null if not found.

**load()** - Returns a proxy (placeholder). Hits database only when you access the object. Throws exception if not found.

**Example:**
```java
// get() - immediate database hit
Employee emp = session.get(Employee.class, 101);
if(emp == null) { // Can check for null
    System.out.println("Not found");
}

// load() - lazy, hits database when you use it
Employee emp = session.load(Employee.class, 101);
// No database hit yet
System.out.println(emp.getName()); // NOW database hit happens
// Throws exception if ID 101 doesn't exist
```

**Use get()** when you're not sure the record exists.
**Use load()** when you're certain it exists and want better performance.

### 23. What is Lazy Loading and Eager Loading?

**Lazy Loading** - Data is loaded only when you actually use it. Like ordering food when hungry, not in advance.

**Eager Loading** - Data is loaded immediately along with the parent object. Like buying groceries for the whole week.

**Example:**
```java
// Employee has many Goals

// Lazy - goals loaded only when accessed
@OneToMany(fetch = FetchType.LAZY)
List<Goal> goals;

Employee emp = session.get(Employee.class, 101);
// Goals NOT loaded yet
System.out.println(emp.getGoals().size()); // NOW goals loaded

// Eager - goals loaded immediately
@OneToMany(fetch = FetchType.EAGER)
List<Goal> goals;

Employee emp = session.get(Employee.class, 101);
// Goals ALREADY loaded, ready to use
```

**In PerformanceTrack:** When showing employee list, use lazy loading for goals. When showing employee details page, use eager loading.

### 24. What is the N+1 Select Problem?

A performance killer where you execute 1 query + N additional queries unnecessarily.

**Example scenario:** Fetch 10 employees and their goals.

**Bad way (N+1 problem):**
```java
List<Employee> emps = session.createQuery("FROM Employee").list(); // 1 query
for(Employee emp : emps) {
    emp.getGoals().size(); // 10 more queries (one per employee)
}
// Total: 11 queries!
```

**Good way:**
```java
// Use JOIN FETCH - single query
List<Employee> emps = session.createQuery(
    "FROM Employee e JOIN FETCH e.goals"
).list();
// Total: 1 query!
```

**In PerformanceTrack:** When generating reports for 100 employees, N+1 problem would execute 101 queries. Using JOIN FETCH makes it just 1 query - much faster!

### 25. What is HQL and how is it different from SQL?

**HQL (Hibernate Query Language)** - Object-oriented query language. Works with Java classes, not tables.

**SQL** - Works with database tables and columns.

**Differences:**

**SQL:** `SELECT * FROM employees WHERE dept_id = 5`
**HQL:** `FROM Employee WHERE department.id = 5`

**SQL:** `SELECT emp_name FROM employees`
**HQL:** `SELECT e.name FROM Employee e`

**Benefits of HQL:**
- Database independent
- Works with objects
- Supports polymorphism

**Example in PerformanceTrack:**
```java
// HQL - works with any database
String hql = "FROM Review WHERE employee.id = :empId";
Query query = session.createQuery(hql);
query.setParameter("empId", 101);
List<Review> reviews = query.list();
```

Shall I continue with more Hibernate and Spring JPA questions?

## **Hibernate Questions (Continued)**

### 26. What is Criteria API in Hibernate?

Criteria API is a programmatic way to build queries using Java code instead of writing strings.

**Benefits:**
- Type-safe (compile-time checking)
- No syntax errors
- Dynamic query building

**Example:**
```java
// HQL way
String hql = "FROM Employee WHERE name = 'John' AND salary > 50000";

// Criteria API way
CriteriaBuilder cb = session.getCriteriaBuilder();
CriteriaQuery<Employee> cq = cb.createQuery(Employee.class);
Root<Employee> root = cq.from(Employee.class);

cq.select(root).where(
    cb.and(
        cb.equal(root.get("name"), "John"),
        cb.gt(root.get("salary"), 50000)
    )
);

List<Employee> employees = session.createQuery(cq).getResultList();
```

**In PerformanceTrack:** When building dynamic filters in your Analytics module (filter by department, date range, score, etc.), Criteria API makes it easy to add/remove conditions based on user selections.

### 27. What is the difference between save() and persist()?

Both insert objects into database, but with subtle differences:

**save()** - Returns the generated ID immediately. Works outside transaction.

**persist()** - Doesn't return anything. Must be inside transaction.

**Example:**
```java
// save() - returns ID
Integer id = (Integer) session.save(employee);
System.out.println("Generated ID: " + id);

// persist() - void return
session.persist(employee);
// Access ID later: employee.getId()
```

**Key difference:** save() is Hibernate-specific, persist() is JPA standard.

**In practice:** Use persist() for better JPA compatibility. Use save() when you need immediate ID access.

### 28. What is the difference between update() and merge()?

Both update objects, but handle detached objects differently:

**update()** - Attaches detached object to session. Throws error if another object with same ID already exists in session.

**merge()** - Copies detached object's state to persistent object. Returns the persistent object.

**Example:**
```java
// User session 1
Session s1 = factory.openSession();
Employee emp = s1.get(Employee.class, 101);
s1.close(); // emp is now DETACHED

// User session 2
Session s2 = factory.openSession();

// update() - reattach same object
s2.update(emp); // emp becomes PERSISTENT again

// merge() - copy to new persistent object
Employee managedEmp = s2.merge(emp); // managedEmp is PERSISTENT
// emp is still DETACHED
```

**In PerformanceTrack:** When a manager edits a review offline and comes back online, use merge() to safely update without conflicts.

### 29. What is First Level Cache in Hibernate?

First Level Cache is automatic session-level caching. It's always enabled.

**How it works:** When you load an object, Hibernate stores it in session. Next time you request same object in same session, it returns from cache - no database hit.

**Example:**
```java
Session session = factory.openSession();

Employee emp1 = session.get(Employee.class, 101); // Database hit
Employee emp2 = session.get(Employee.class, 101); // From cache, NO database hit

System.out.println(emp1 == emp2); // true - same object
session.close(); // Cache cleared
```

**In PerformanceTrack:** When generating a report that references same employee multiple times, first level cache ensures database is hit only once per employee.

### 30. What is Second Level Cache in Hibernate?

Second Level Cache is SessionFactory-level caching. Shared across all sessions. Must be explicitly configured.

**First Level vs Second Level:**
- First Level: Session scope, automatic
- Second Level: Application scope, manual configuration

**Example:**
```java
// Session 1
Session s1 = factory.openSession();
Employee emp1 = s1.get(Employee.class, 101); // Database hit
s1.close();

// Session 2 - different session
Session s2 = factory.openSession();
Employee emp2 = s2.get(Employee.class, 101); // From 2nd level cache, NO database hit
s2.close();
```

**Configuration:**
```java
@Entity
@Cacheable
@Cache(usage = CacheConcurrencyStrategy.READ_WRITE)
public class Employee {
    // fields
}
```

**In PerformanceTrack:** Cache frequently accessed data like department lists, goal templates - data that doesn't change often.

### 31. What are the different cache concurrency strategies?

When using Second Level Cache, you choose how to handle concurrent access:

**READ_ONLY** - Data never changes. Best performance. Use for reference data.

**READ_WRITE** - Data changes occasionally. Good for most use cases.

**NONSTRICT_READ_WRITE** - Rare updates. Slight chance of stale data but better performance.

**TRANSACTIONAL** - Strict consistency. Slower but guarantees no stale data.

**Example in PerformanceTrack:**

```java
// Department list - rarely changes
@Cache(usage = CacheConcurrencyStrategy.READ_ONLY)
public class Department { }

// Employee data - changes regularly
@Cache(usage = CacheConcurrencyStrategy.READ_WRITE)
public class Employee { }

// Goal templates - occasional updates, ok if slightly stale
@Cache(usage = CacheConcurrencyStrategy.NONSTRICT_READ_WRITE)
public class GoalTemplate { }
```

### 32. What is Hibernate Configuration file and what does it contain?

`hibernate.cfg.xml` is the main configuration file. It contains database connection details and Hibernate settings.

**Key elements:**

**Database connection:** URL, username, password, driver
**Hibernate properties:** Dialect, show SQL, format SQL
**Mapping files:** Which entity classes to use
**Cache settings:** Second level cache configuration

**Example:**
```xml
<hibernate-configuration>
    <session-factory>
        <!-- Database connection -->
        <property name="connection.driver_class">com.mysql.jdbc.Driver</property>
        <property name="connection.url">jdbc:mysql://localhost:3306/performancetrack</property>
        <property name="connection.username">root</property>
        <property name="connection.password">password</property>
        
        <!-- Hibernate properties -->
        <property name="dialect">org.hibernate.dialect.MySQL5Dialect</property>
        <property name="show_sql">true</property>
        <property name="hbm2ddl.auto">update</property>
        
        <!-- Mapping -->
        <mapping class="com.cognizant.model.Employee"/>
    </session-factory>
</hibernate-configuration>
```

### 33. What is hbm2ddl.auto and what are its values?

`hbm2ddl.auto` controls how Hibernate manages database schema automatically.

**Values:**

**create** - Drops existing tables and creates new ones every time. USE ONLY IN DEVELOPMENT.

**create-drop** - Creates tables on startup, drops on shutdown. For testing.

**update** - Updates schema if entity classes change. Safest for development.

**validate** - Only validates schema matches entities. Doesn't change anything. Use in production.

**none** - Does nothing. Manage schema manually.

**Example:**
```xml
<!-- Development -->
<property name="hbm2ddl.auto">update</property>

<!-- Production -->
<property name="hbm2ddl.auto">validate</property>
```

**In PerformanceTrack:** Use "update" while developing. When deploying to production, use "validate" or "none" to prevent accidental data loss.

### 34. What are Hibernate mapping annotations?

Annotations configure how Java classes map to database tables.

**Common annotations:**

**@Entity** - Marks class as database table

**@Table** - Specifies table name

**@Id** - Primary key field

**@GeneratedValue** - Auto-generate ID values

**@Column** - Column details (name, length, nullable)

**Example from PerformanceTrack:**
```java
@Entity
@Table(name = "performance_reviews")
public class Review {
    
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    @Column(name = "review_id")
    private Integer id;
    
    @Column(name = "review_score", nullable = false)
    private Integer score;
    
    @Column(name = "comments", length = 1000)
    private String comments;
}
```

### 35. What are the different ID generation strategies?

`@GeneratedValue` has different strategies to generate primary keys:

**AUTO** - Hibernate chooses based on database. Default option.

**IDENTITY** - Uses database auto-increment. Works for MySQL, SQL Server.

**SEQUENCE** - Uses database sequence. Works for Oracle, PostgreSQL.

**TABLE** - Uses a separate table to generate IDs. Works everywhere but slower.

**Example:**
```java
// MySQL - use IDENTITY
@Id
@GeneratedValue(strategy = GenerationType.IDENTITY)
private Integer id;

// Oracle - use SEQUENCE
@Id
@GeneratedValue(strategy = GenerationType.SEQUENCE, generator = "emp_seq")
@SequenceGenerator(name = "emp_seq", sequenceName = "employee_sequence")
private Integer id;

// Universal - use TABLE
@Id
@GeneratedValue(strategy = GenerationType.TABLE, generator = "emp_gen")
@TableGenerator(name = "emp_gen", table = "id_generator")
private Integer id;
```

**In PerformanceTrack:** If using MySQL, IDENTITY is simplest and fastest.

### 36. What are the different types of relationships in Hibernate?

Four main relationship types:

**@OneToOne** - One record relates to exactly one other record.
Example: Employee ↔ EmployeeProfile (one employee has one profile)

**@OneToMany** - One record relates to many others.
Example: Employee → Goals (one employee has many goals)

**@ManyToOne** - Many records relate to one.
Example: Goals → Employee (many goals belong to one employee)

**@ManyToMany** - Many records relate to many others.
Example: Employees ↔ Skills (one employee has many skills, one skill belongs to many employees)

**PerformanceTrack examples:**
```java
// Employee has many Reviews
@OneToMany(mappedBy = "employee")
private List<Review> reviews;

// Review belongs to one Employee
@ManyToOne
@JoinColumn(name = "employee_id")
private Employee employee;

// Employee has many Skills, Skill has many Employees
@ManyToMany
@JoinTable(name = "employee_skills",
    joinColumns = @JoinColumn(name = "employee_id"),
    inverseJoinColumns = @JoinColumn(name = "skill_id"))
private List<Skill> skills;
```

### 37. What is the difference between Unidirectional and Bidirectional relationships?

**Unidirectional** - Only one side knows about the relationship.

**Bidirectional** - Both sides know about each other.

**Example:**

**Unidirectional (Employee knows Goals, but Goals don't know Employee):**
```java
public class Employee {
    @OneToMany
    private List<Goal> goals; // Employee knows its goals
}

public class Goal {
    // No reference to Employee
}
```

**Bidirectional (Both know each other):**
```java
public class Employee {
    @OneToMany(mappedBy = "employee")
    private List<Goal> goals;
}

public class Goal {
    @ManyToOne
    private Employee employee; // Goal knows its employee
}
```

**In PerformanceTrack:** Use bidirectional when you need to navigate both ways. From Employee, find all Goals. From Goal, find which Employee owns it.

### 38. What is mappedBy in Hibernate?

`mappedBy` indicates which side owns the relationship. It prevents creating duplicate foreign key columns.

**Without mappedBy:**
Hibernate creates foreign keys on BOTH tables (redundant!)

**With mappedBy:**
Hibernate creates foreign key only on the owning side.

**Example:**
```java
// Employee (non-owning side)
@OneToMany(mappedBy = "employee") // "employee" refers to field name in Goal class
private List<Goal> goals;

// Goal (owning side - has foreign key)
@ManyToOne
@JoinColumn(name = "employee_id") // This creates the actual foreign key column
private Employee employee;
```

**Rule:** The side with mappedBy is NOT the owner. The other side owns the relationship.

**In PerformanceTrack:** In Employee-Goals relationship, Goal table has the employee_id foreign key, so Goal owns the relationship.

### 39. What is Cascading in Hibernate?

Cascading means when you perform an operation on parent, it automatically applies to children.

**Cascade types:**

**CascadeType.PERSIST** - Save parent, children auto-save
**CascadeType.REMOVE** - Delete parent, children auto-delete
**CascadeType.MERGE** - Merge parent, children auto-merge
**CascadeType.REFRESH** - Refresh parent, children auto-refresh
**CascadeType.DETACH** - Detach parent, children auto-detach
**CascadeType.ALL** - All of the above

**Example:**
```java
public class Employee {
    @OneToMany(mappedBy = "employee", cascade = CascadeType.ALL)
    private List<Goal> goals;
}

// Now:
Employee emp = new Employee();
emp.addGoal(new Goal("Complete project"));
session.save(emp); // Saves Employee AND Goal automatically
```

**Warning:** Be careful with CascadeType.REMOVE. Deleting an employee would delete all their goals!

**In PerformanceTrack:** Use cascade for saving reviews with comments together. Don't use it for deletions - you want to keep historical data.

### 40. What is FetchType in Hibernate?

FetchType controls when related data is loaded.

**EAGER** - Load immediately with parent.
**LAZY** - Load only when accessed.

**Example:**
```java
// Reviews loaded immediately with Employee
@OneToMany(fetch = FetchType.EAGER)
private List<Review> reviews;

// Goals loaded only when accessed
@OneToMany(fetch = FetchType.LAZY)
private List<Goal> goals;

Employee emp = session.get(Employee.class, 101);
// Reviews already loaded
// Goals NOT loaded yet

emp.getGoals().size(); // NOW goals are loaded
```

**Default behavior:**
- @OneToOne, @ManyToOne → EAGER
- @OneToMany, @ManyToMany → LAZY

**Best practice:** Use LAZY as default. Use EAGER only when you're certain you'll always need the data.

**In PerformanceTrack:** When displaying employee list, use LAZY for goals. When showing employee detail page, use EAGER or fetch manually.

### 41. What is the difference between @JoinColumn and @JoinTable?

**@JoinColumn** - Used for @OneToOne and @ManyToOne. Creates foreign key column in current table.

**@JoinTable** - Used for @ManyToMany. Creates a separate junction/mapping table.

**Example:**

**@JoinColumn (ManyToOne):**
```java
public class Review {
    @ManyToOne
    @JoinColumn(name = "employee_id") // Creates employee_id column in reviews table
    private Employee employee;
}
```
Table structure: `reviews` table has `employee_id` column.

**@JoinTable (ManyToMany):**
```java
public class Employee {
    @ManyToMany
    @JoinTable(
        name = "employee_skills", // New table name
        joinColumns = @JoinColumn(name = "employee_id"),
        inverseJoinColumns = @JoinColumn(name = "skill_id")
    )
    private List<Skill> skills;
}
```
Table structure: Creates new `employee_skills` table with columns `employee_id` and `skill_id`.

### 42. What is Hibernate Dialect?

Dialect tells Hibernate which database you're using so it can generate correct SQL syntax.

Different databases have different SQL syntax. Hibernate Dialect translates HQL to database-specific SQL.

**Example:**
```xml
<!-- MySQL -->
<property name="dialect">org.hibernate.dialect.MySQL5Dialect</property>

<!-- Oracle -->
<property name="dialect">org.hibernate.dialect.Oracle10gDialect</property>

<!-- PostgreSQL -->
<property name="dialect">org.hibernate.dialect.PostgreSQLDialect</property>

<!-- SQL Server -->
<property name="dialect">org.hibernate.dialect.SQLServerDialect</property>
```

**Why it matters:** 
- MySQL uses `LIMIT` for pagination
- Oracle uses `ROWNUM`
- SQL Server uses `TOP`

Dialect handles these differences automatically.

**In PerformanceTrack:** Set the correct dialect in hibernate.cfg.xml based on your database. If you change databases later, just change the dialect - no code changes needed!

### 43. What is the difference between openSession() and getCurrentSession()?

**openSession()** - Always creates a new session. You must close it manually.

**getCurrentSession()** - Returns existing session if available, creates new if not. Auto-closes when transaction ends.

**Example:**
```java
// openSession() - manual management
Session session = factory.openSession();
session.beginTransaction();
// do work
session.getTransaction().commit();
session.close(); // Must close manually

// getCurrentSession() - automatic management
Session session = factory.getCurrentSession();
session.beginTransaction();
// do work
session.getTransaction().commit();
// Auto-closes, no need to call close()
```

**In PerformanceTrack:** Use getCurrentSession() in Spring applications - it integrates with Spring's transaction management. Use openSession() when you need fine control over session lifecycle.

### 44. What is a Named Query in Hibernate?

Named Query is a predefined HQL query with a name. You write it once and reuse it many times.

**Benefits:**
- Compile-time checking
- Centralized query management
- Reusable across application

**Example:**
```java
@Entity
@NamedQueries({
    @NamedQuery(
        name = "Review.findByEmployee",
        query = "FROM Review r WHERE r.employee.id = :empId"
    ),
    @NamedQuery(
        name = "Review.findByScoreRange",
        query = "FROM Review r WHERE r.score BETWEEN :minScore AND :maxScore"
    )
})
public class Review {
    // fields
}

// Using named query
Query query = session.getNamedQuery("Review.findByEmployee");
query.setParameter("empId", 101);
List<Review> reviews = query.list();
```

**In PerformanceTrack:** Define named queries for common reports like "monthly performance summary", "top performers", "pending reviews" - use them wherever needed.

### 45. What is the difference between Hibernate and JDBC batch processing?

Both improve performance for bulk operations, but Hibernate's is easier.

**JDBC batch:**
```java
PreparedStatement ps = conn.prepareStatement("INSERT INTO goals VALUES (?, ?)");
for(int i = 1; i <= 1000; i++) {
    ps.setInt(1, i);
    ps.setString(2, "Goal " + i);
    ps.addBatch();
    if(i % 100 == 0) {
        ps.executeBatch();
    }
}
```

**Hibernate batch:**
```java
Session session = factory.openSession();
Transaction tx = session.beginTransaction();
for(int i = 1; i <= 1000; i++) {
    Goal goal = new Goal("Goal " + i);
    session.save(goal);
    if(i % 100 == 0) {
        session.flush(); // Send batch to database
        session.clear(); // Clear session cache
    }
}
tx.commit();
```

**Configuration:**
```xml
<property name="hibernate.jdbc.batch_size">100</property>
```

**In PerformanceTrack:** When importing 5000 employee goals from Excel, batching makes it 50x faster and prevents memory issues.

---

## **Spring Data JPA Questions**

### 46. What is Spring Data JPA?

Spring Data JPA is a layer on top of JPA/Hibernate that makes database operations even simpler.

**Key benefit:** You write interfaces, Spring generates implementations automatically.

**Example:**

**Without Spring Data JPA (traditional Hibernate):**
```java
public class EmployeeDAO {
    public Employee findById(Integer id) {
        Session session = factory.openSession();
        Employee emp = session.get(Employee.class, id);
        session.close();
        return emp;
    }
    
    public List<Employee> findAll() {
        Session session = factory.openSession();
        List<Employee> emps = session.createQuery("FROM Employee").list();
        session.close();
        return emps;
    }
    // Many more methods...
}
```

**With Spring Data JPA:**
```java
public interface EmployeeRepository extends JpaRepository<Employee, Integer> {
    // That's it! Spring provides findById, findAll, save, delete, etc.
}
```

**In PerformanceTrack:** Your `ReportRepository` extends `JpaRepository` - that's why you get methods like `findAll()`, `save()` without writing any code!

### 47. What is JpaRepository and what does it provide?

JpaRepository is the main interface in Spring Data JPA. It provides ready-made methods for CRUD operations.

**Built-in methods:**
- `save(entity)` - Insert or update
- `findById(id)` - Find by primary key
- `findAll()` - Get all records
- `deleteById(id)` - Delete by ID
- `count()` - Count total records
- `existsById(id)` - Check if exists

**Example:**
```java
public interface ReviewRepository extends JpaRepository<Review, Integer> {
    // Inherits all methods above automatically
}

// Usage in service
@Service
public class ReviewService {
    @Autowired
    private ReviewRepository reviewRepo;
    
    public Review getReview(Integer id) {
        return reviewRepo.findById(id).orElse(null);
    }
    
    public List<Review> getAllReviews() {
        return reviewRepo.findAll();
    }
    
    public Review saveReview(Review review) {
        return reviewRepo.save(review);
    }
}
```

**In PerformanceTrack:** All your repositories (ReportRepository, GoalRepository, etc.) extend JpaRepository - giving you instant CRUD capabilities.

### 48. What is the difference between CrudRepository, JpaRepository, and PagingAndSortingRepository?

Three repository interfaces with increasing functionality:

**CrudRepository** - Basic CRUD operations
- save, findById, findAll, delete, count

**PagingAndSortingRepository** - CRUD + pagination + sorting
- All CRUD methods
- `findAll(Sort sort)`
- `findAll(Pageable pageable)`

**JpaRepository** - Everything + JPA specific methods
- All above methods
- `flush()`, `saveAndFlush()`
- `deleteInBatch()`
- `getOne()` (proxy-based)

**Hierarchy:**
```
CrudRepository
    ↑
PagingAndSortingRepository
    ↑
JpaRepository (Best to use)
```

**Example:**
```java
// Best practice - use JpaRepository
public interface EmployeeRepository extends JpaRepository<Employee, Integer> {
    // Get all features
}

// Usage with pagination
Pageable pageable = PageRequest.of(0, 10, Sort.by("name"));
Page<Employee> employees = employeeRepo.findAll(pageable);
```

**In PerformanceTrack:** Use JpaRepository for all repositories. When showing reports with 1000 records, pagination makes it manageable - 50 records per page.

### 49. What are derived query methods in Spring Data JPA?

Derived query methods are methods Spring Data JPA creates automatically based on method names.

**Naming convention:**
`findBy` + `PropertyName` + `Condition`

**Examples:**
```java
public interface EmployeeRepository extends JpaRepository<Employee, Integer> {
    
    // Find by single property
    List<Employee> findByName(String name);
    // SELECT * FROM employees WHERE name = ?
    
    // Find by multiple properties
    List<Employee> findByNameAndDepartment(String name, String dept);
    // SELECT * FROM employees WHERE name = ? AND department = ?
    
    // Greater than
    List<Employee> findBySalaryGreaterThan(Double salary);
    // SELECT * FROM employees WHERE salary > ?
    
    // Like query
    List<Employee> findByNameContaining(String keyword);
    // SELECT * FROM employees WHERE name LIKE %keyword%
    
    // Order by
    List<Employee> findByDepartmentOrderBySalaryDesc(String dept);
    // SELECT * FROM employees WHERE department = ? ORDER BY salary DESC
}
```

**In PerformanceTrack:**
```java
public interface ReviewRepository extends JpaRepository<Review, Integer> {
    List<Review> findByEmployeeId(Integer empId);
    List<Review> findByScoreGreaterThanEqual(Integer minScore);
    List<Review> findByReviewDateBetween(LocalDate start, LocalDate end);
}
```

Spring automatically generates the implementation!

### 50. What are the common keywords used in derived query methods?

Spring Data JPA supports many keywords for building query methods:

**Equality:**
- `findByName` → WHERE name = ?
- `findByNameIs` → WHERE name = ?
- `findByNameEquals` → WHERE name = ?

**Comparison:**
- `findBySalaryGreaterThan` → WHERE salary > ?
- `findBySalaryLessThan` → WHERE salary < ?
- `findBySalaryBetween` → WHERE salary BETWEEN ? AND ?

**String matching:**
- `findByNameContaining` → WHERE name LIKE %?%
- `findByNameStartingWith` → WHERE name LIKE ?%
- `findByNameEndingWith` → WHERE name LIKE %?

**Null checks:**
- `findByEmailIsNull` → WHERE email IS NULL
- `findByEmailIsNotNull` → WHERE email IS NOT NULL

**Collections:**
- `findByDepartmentIn(List<String> depts)` → WHERE department IN (?)

**Boolean:**
- `findByActiveTrue` → WHERE active = true
- `findByActiveFalse` → WHERE active = false

**Ordering:**
- `findByNameOrderBySalaryDesc` → ORDER BY salary DESC

**Limiting:**
- `findTop10ByOrderBySalaryDesc` → LIMIT 10

**Example in PerformanceTrack:**
```java
public interface GoalRepository extends JpaRepository<Goal, Integer> {
    List<Goal> findByStatus(String status);
    List<Goal> findByTargetDateBefore(LocalDate date);
    List<Goal> findByTitleContainingIgnoreCase(String keyword);
    List<Goal> findTop5ByEmployeeIdOrderByCreatedDateDesc(Integer empId);
}
```

Shall I continue with more Spring Data JPA questions?

## **Spring Data JPA Questions (Continued)**

### 51. What is @Query annotation and when do we use it?

`@Query` allows you to write custom JPQL or native SQL queries when derived query method names become too complex or unclear.

**When to use:**
- Complex queries that are hard to express in method names
- Join queries across multiple tables
- Aggregation queries (SUM, AVG, COUNT)
- Custom projections

**Example:**

**JPQL Query:**
```java
public interface ReviewRepository extends JpaRepository<Review, Integer> {
    
    @Query("SELECT r FROM Review r WHERE r.employee.id = :empId AND r.score >= :minScore")
    List<Review> findGoodReviewsByEmployee(@Param("empId") Integer empId, 
                                           @Param("minScore") Integer minScore);
    
    @Query("SELECT AVG(r.score) FROM Review r WHERE r.employee.department = :dept")
    Double getAverageDepartmentScore(@Param("dept") String dept);
    
    @Query("SELECT r FROM Review r JOIN FETCH r.employee WHERE r.reviewDate >= :date")
    List<Review> findRecentReviewsWithEmployees(@Param("date") LocalDate date);
}
```

**Native SQL Query:**
```java
@Query(value = "SELECT * FROM reviews WHERE score > ?1", nativeQuery = true)
List<Review> findHighScoreReviewsNative(Integer minScore);
```

**In PerformanceTrack Analytics module:**
```java
@Query("SELECT NEW com.cognizant.dto.PerformanceStats(e.department, AVG(r.score), COUNT(r)) " +
       "FROM Review r JOIN r.employee e " +
       "GROUP BY e.department")
List<PerformanceStats> getDepartmentPerformanceStats();
```

This is much cleaner than trying to create a method name like `findAverageScoreAndCountGroupedByDepartment`!

### 52. What is the difference between JPQL and Native SQL queries?

**JPQL (Java Persistence Query Language):**
- Works with entity classes and properties
- Database independent
- Type-safe
- Recommended approach

**Native SQL:**
- Works with actual database tables and columns
- Database dependent
- Direct database access
- Use only when JPQL can't do what you need

**Example:**

**JPQL:**
```java
@Query("SELECT e FROM Employee e WHERE e.name = :name")
Employee findByNameJPQL(@Param("name") String name);
// Works with any database - Hibernate translates to correct SQL
```

**Native SQL:**
```java
@Query(value = "SELECT * FROM employees WHERE emp_name = :name", nativeQuery = true)
Employee findByNameNative(@Param("name") String name);
// Tied to specific table/column names
```

**When to use Native SQL:**
- Database-specific features (MySQL's MATCH AGAINST, Oracle's ROWNUM)
- Complex stored procedures
- Performance optimization with database hints
- Legacy database with weird table structures

**In PerformanceTrack:** Use JPQL for 95% of queries. Use Native SQL only for complex analytics reports that need database-specific optimizations.

### 53. What is @Modifying annotation?

`@Modifying` is used with `@Query` for UPDATE and DELETE operations. It tells Spring Data JPA that this query modifies data.

**Without @Modifying, Spring expects SELECT queries only.**

**Example:**
```java
public interface GoalRepository extends JpaRepository<Goal, Integer> {
    
    // Update query
    @Modifying
    @Query("UPDATE Goal g SET g.status = :status WHERE g.id = :goalId")
    int updateGoalStatus(@Param("goalId") Integer goalId, 
                         @Param("status") String status);
    
    // Delete query
    @Modifying
    @Query("DELETE FROM Goal g WHERE g.employee.id = :empId AND g.status = :status")
    int deleteCompletedGoals(@Param("empId") Integer empId, 
                             @Param("status") String status);
    
    // Bulk update
    @Modifying
    @Query("UPDATE Goal g SET g.status = 'Overdue' WHERE g.targetDate < CURRENT_DATE AND g.status = 'In Progress'")
    int markOverdueGoals();
}
```

**Important:** Use with `@Transactional` in service layer:
```java
@Service
public class GoalService {
    @Autowired
    private GoalRepository goalRepo;
    
    @Transactional
    public void approveGoal(Integer goalId) {
        goalRepo.updateGoalStatus(goalId, "Approved");
    }
}
```

**In PerformanceTrack:** When a manager bulk-approves 50 goals, use @Modifying for efficient single UPDATE query instead of 50 separate updates.

### 54. What is the difference between @Param and positional parameters?

Two ways to pass parameters to @Query:

**@Param (Named parameters)** - Use parameter names. More readable.

**Positional parameters** - Use ?1, ?2, ?3. Order-based.

**Example:**

**Named parameters (Recommended):**
```java
@Query("SELECT e FROM Employee e WHERE e.name = :name AND e.department = :dept")
List<Employee> findByNameAndDept(@Param("name") String name, 
                                  @Param("dept") String dept);
// Clear and readable
```

**Positional parameters:**
```java
@Query("SELECT e FROM Employee e WHERE e.name = ?1 AND e.department = ?2")
List<Employee> findByNameAndDept(String name, String dept);
// Order matters! ?1 is first param, ?2 is second
```

**Why @Param is better:**
- More readable
- Order doesn't matter
- Easier to maintain when query changes

**In PerformanceTrack:** Always use @Param for clarity, especially in complex analytics queries with multiple parameters.

### 55. What is Pagination in Spring Data JPA?

Pagination divides large result sets into smaller pages, improving performance and user experience.

**Key interfaces:**
- `Pageable` - Defines page number, size, and sorting
- `Page<T>` - Contains page data plus metadata (total pages, total elements)

**Example:**
```java
public interface EmployeeRepository extends JpaRepository<Employee, Integer> {
    Page<Employee> findByDepartment(String dept, Pageable pageable);
}

// Service layer
@Service
public class EmployeeService {
    @Autowired
    private EmployeeRepository employeeRepo;
    
    public Page<Employee> getEmployeesByDepartment(String dept, int pageNum, int pageSize) {
        Pageable pageable = PageRequest.of(pageNum, pageSize, Sort.by("name"));
        return employeeRepo.findByDepartment(dept, pageable);
    }
}

// Controller
@GetMapping("/employees")
public Page<Employee> getEmployees(
    @RequestParam String dept,
    @RequestParam(defaultValue = "0") int page,
    @RequestParam(defaultValue = "10") int size) {
    
    return employeeService.getEmployeesByDepartment(dept, page, size);
}
```

**Page object provides:**
```java
Page<Employee> page = employeeRepo.findAll(pageable);
page.getContent(); // List of employees on this page
page.getTotalElements(); // Total employees in database
page.getTotalPages(); // Total number of pages
page.getNumber(); // Current page number
page.hasNext(); // Is there a next page?
page.hasPrevious(); // Is there a previous page?
```

**In PerformanceTrack Analytics:** When displaying 5000 performance reviews, show 50 per page. Users can navigate pages smoothly without loading all 5000 at once.

### 56. What is Sorting in Spring Data JPA?

Sorting orders query results based on one or more properties.

**Two ways to sort:**

**1. Using Sort in repository method:**
```java
public interface EmployeeRepository extends JpaRepository<Employee, Integer> {
    List<Employee> findByDepartment(String dept, Sort sort);
}

// Usage
Sort sort = Sort.by("salary").descending();
List<Employee> employees = employeeRepo.findByDepartment("IT", sort);

// Multiple properties
Sort sort = Sort.by("department").ascending()
                .and(Sort.by("salary").descending());
```

**2. Using method name:**
```java
public interface EmployeeRepository extends JpaRepository<Employee, Integer> {
    List<Employee> findByDepartmentOrderBySalaryDesc(String dept);
    List<Employee> findByDepartmentOrderByNameAscSalaryDesc(String dept);
}
```

**3. Combined with Pagination:**
```java
Pageable pageable = PageRequest.of(0, 10, Sort.by("salary").descending());
Page<Employee> topEarners = employeeRepo.findAll(pageable);
```

**In PerformanceTrack:**
```java
// Top performers report - sorted by score descending
Sort sort = Sort.by("score").descending();
List<Review> topPerformers = reviewRepo.findByReviewYear(2024, sort);

// Employee list - sorted by department, then name
Sort sort = Sort.by("department").and(Sort.by("name"));
```

### 57. What are Projections in Spring Data JPA?

Projections allow you to retrieve only specific fields instead of entire entities, improving performance.

**Problem:** Loading entire Employee entity when you only need name and email is wasteful.

**Types of Projections:**

**1. Interface-based Projection:**
```java
// Define projection interface
public interface EmployeeSummary {
    String getName();
    String getEmail();
}

// Repository
public interface EmployeeRepository extends JpaRepository<Employee, Integer> {
    List<EmployeeSummary> findByDepartment(String dept);
}

// Usage
List<EmployeeSummary> summaries = employeeRepo.findByDepartment("IT");
for(EmployeeSummary summary : summaries) {
    System.out.println(summary.getName()); // Only name and email loaded
}
```

**2. Class-based Projection (DTO):**
```java
// DTO class
public class EmployeeDTO {
    private String name;
    private String email;
    
    public EmployeeDTO(String name, String email) {
        this.name = name;
        this.email = email;
    }
    // getters
}

// Repository
@Query("SELECT new com.cognizant.dto.EmployeeDTO(e.name, e.email) FROM Employee e WHERE e.department = :dept")
List<EmployeeDTO> findEmployeeSummaries(@Param("dept") String dept);
```

**3. Dynamic Projection:**
```java
public interface EmployeeRepository extends JpaRepository<Employee, Integer> {
    <T> List<T> findByDepartment(String dept, Class<T> type);
}

// Usage - choose projection at runtime
List<EmployeeSummary> summaries = employeeRepo.findByDepartment("IT", EmployeeSummary.class);
List<Employee> full = employeeRepo.findByDepartment("IT", Employee.class);
```

**In PerformanceTrack Analytics:**
```java
// Performance summary for dashboard - only need essential fields
public interface PerformanceSummary {
    String getEmployeeName();
    Double getAverageScore();
    Integer getReviewCount();
}

@Query("SELECT e.name as employeeName, AVG(r.score) as averageScore, COUNT(r) as reviewCount " +
       "FROM Review r JOIN r.employee e GROUP BY e.name")
List<PerformanceSummary> getPerformanceDashboard();
```

This loads only 3 fields instead of all Employee and Review fields - much faster!

### 58. What is @EntityGraph in Spring Data JPA?

`@EntityGraph` solves the N+1 select problem by specifying which associations to fetch eagerly in a single query.

**Problem:** With lazy loading, fetching 10 employees and their goals results in 11 queries (1 for employees + 10 for each employee's goals).

**Solution:**
```java
@Entity
public class Employee {
    @Id
    private Integer id;
    private String name;
    
    @OneToMany(mappedBy = "employee", fetch = FetchType.LAZY)
    private List<Goal> goals;
}

// Repository with EntityGraph
public interface EmployeeRepository extends JpaRepository<Employee, Integer> {
    
    @EntityGraph(attributePaths = {"goals"})
    List<Employee> findAll();
    
    @EntityGraph(attributePaths = {"goals", "reviews"})
    Employee findById(Integer id);
    
    @EntityGraph(attributePaths = {"goals.comments"}) // Nested associations
    List<Employee> findByDepartment(String dept);
}
```

**What it does:**
```java
List<Employee> employees = employeeRepo.findAll();
// Single query with LEFT JOIN to fetch employees AND goals together
// No N+1 problem!
```

**Named EntityGraph:**
```java
@Entity
@NamedEntityGraph(
    name = "Employee.withGoalsAndReviews",
    attributeNodes = {
        @NamedAttributeNode("goals"),
        @NamedAttributeNode("reviews")
    }
)
public class Employee {
    // fields
}

// Repository
@EntityGraph("Employee.withGoalsAndReviews")
List<Employee> findByDepartment(String dept);
```

**In PerformanceTrack:** When generating reports showing employees with their goals and reviews, EntityGraph fetches everything in one query instead of hundreds.

### 59. What is the difference between @Transactional in Spring and Hibernate?

`@Transactional` manages database transactions - grouping multiple operations into one atomic unit.

**Without @Transactional:**
```java
public void transferMoney(int from, int to, double amount) {
    accountRepo.withdraw(from, amount); // Succeeds
    accountRepo.deposit(to, amount); // Fails
    // Money deducted but not deposited - data inconsistency!
}
```

**With @Transactional:**
```java
@Transactional
public void transferMoney(int from, int to, double amount) {
    accountRepo.withdraw(from, amount);
    accountRepo.deposit(to, amount);
    // Both succeed together or both rollback - data stays consistent!
}
```

**Spring @Transactional features:**

**Propagation** - How transaction behaves when called from another transaction
**Isolation** - How transactions see each other's changes
**Rollback rules** - Which exceptions trigger rollback
**Timeout** - Maximum transaction duration

**Example:**
```java
@Service
public class ReviewService {
    
    @Transactional(
        propagation = Propagation.REQUIRED,
        isolation = Isolation.READ_COMMITTED,
        rollbackFor = Exception.class,
        timeout = 30
    )
    public void submitReview(Review review) {
        reviewRepo.save(review); // Step 1
        employeeRepo.updateAverageScore(review.getEmployeeId()); // Step 2
        notificationService.sendEmail(review); // Step 3
        // If any step fails, all rollback
    }
}
```

**In PerformanceTrack:** When submitting a performance review:
1. Save review
2. Update employee's average score
3. Create notification
4. Log audit entry

All must succeed together. If notification fails, don't save incomplete review.

### 60. What are Transaction Propagation levels?

Propagation controls how transactions behave when one transactional method calls another.

**REQUIRED (Default)** - Use existing transaction, create new if none exists.
```java
@Transactional(propagation = Propagation.REQUIRED)
public void methodA() {
    methodB(); // Joins methodA's transaction
}

@Transactional(propagation = Propagation.REQUIRED)
public void methodB() {
    // Same transaction as methodA
}
```

**REQUIRES_NEW** - Always create new transaction. Suspend existing one.
```java
@Transactional
public void methodA() {
    methodB(); // methodB runs in separate transaction
}

@Transactional(propagation = Propagation.REQUIRES_NEW)
public void methodB() {
    // New transaction, independent of methodA
    // If methodB succeeds but methodA fails, methodB changes are saved
}
```

**SUPPORTS** - Use transaction if exists, run without transaction if not.
```java
@Transactional(propagation = Propagation.SUPPORTS)
public void readData() {
    // Joins transaction if called from transactional method
    // Runs without transaction if called directly
}
```

**MANDATORY** - Must be called within existing transaction, else throw exception.
```java
@Transactional(propagation = Propagation.MANDATORY)
public void mustBeInTransaction() {
    // Throws exception if no transaction exists
}
```

**NEVER** - Must NOT be called within transaction, else throw exception.
```java
@Transactional(propagation = Propagation.NEVER)
public void mustBeNonTransactional() {
    // Throws exception if transaction exists
}
```

**In PerformanceTrack:**
```java
@Transactional
public void submitReview(Review review) {
    reviewRepo.save(review);
    logAuditEntry(review); // Separate transaction - even if review fails, audit is saved
}

@Transactional(propagation = Propagation.REQUIRES_NEW)
public void logAuditEntry(Review review) {
    auditRepo.save(new AuditLog(review));
    // Independent transaction
}
```

### 61. What are Transaction Isolation levels?

Isolation controls how concurrent transactions see each other's changes. Prevents issues like dirty reads, phantom reads.

**READ_UNCOMMITTED** - Can read uncommitted changes from other transactions. Fastest but risky.

**READ_COMMITTED** - Can only read committed data. Prevents dirty reads.

**REPEATABLE_READ** - Same query returns same results even if data changes. Prevents non-repeatable reads.

**SERIALIZABLE** - Full isolation. Transactions run as if serial. Slowest but safest.

**Common problems:**

**Dirty Read** - Reading uncommitted data that gets rolled back.
```java
// Transaction 1
employee.setSalary(100000); // Not committed yet

// Transaction 2 reads 100000
// Transaction 1 rollback
// Transaction 2 has wrong data!
```

**Non-repeatable Read** - Same query returns different results.
```java
// Transaction 1
int salary = employee.getSalary(); // 50000

// Transaction 2 updates salary to 60000 and commits

// Transaction 1
int salary2 = employee.getSalary(); // Now 60000!
// Same query, different result
```

**Phantom Read** - New rows appear in second query.
```java
// Transaction 1
List<Employee> emps = findByDept("IT"); // 10 employees

// Transaction 2 adds new IT employee and commits

// Transaction 1
List<Employee> emps2 = findByDept("IT"); // Now 11 employees!
// Phantom employee appeared
```

**Example:**
```java
@Transactional(isolation = Isolation.READ_COMMITTED)
public void calculateBonus() {
    // Won't read uncommitted salary changes
    // Prevents dirty reads
}

@Transactional(isolation = Isolation.REPEATABLE_READ)
public void generateMonthlyReport() {
    // Data stays consistent throughout transaction
    // Prevents non-repeatable reads
}
```

**In PerformanceTrack:** Use READ_COMMITTED for most operations. Use REPEATABLE_READ for reports to ensure consistent data throughout generation.

### 62. What is the difference between save() and saveAndFlush()?

Both save entities, but timing differs:

**save()** - Saves to persistence context (Hibernate cache), might not hit database immediately.

**saveAndFlush()** - Saves AND immediately flushes to database.

**Example:**
```java
@Service
public class GoalService {
    
    @Autowired
    private GoalRepository goalRepo;
    
    @Transactional
    public void createGoal() {
        Goal goal = new Goal("Complete project");
        
        goalRepo.save(goal);
        // Saved in Hibernate cache
        // Database might not have it yet
        // ID might not be generated yet (depends on strategy)
        
        goalRepo.saveAndFlush(goal);
        // Immediately written to database
        // ID definitely generated
        // Can query database and see the goal
    }
}
```

**When to use saveAndFlush():**
- Need ID immediately (with IDENTITY generation)
- Must verify database constraints right away
- Synchronizing with external systems
- Debugging to see SQL immediately

**When to use save():**
- Normal operations (default choice)
- Batching saves for performance
- Let Hibernate optimize flush timing

**In PerformanceTrack:**
```java
@Transactional
public void bulkCreateGoals(List<Goal> goals) {
    for(Goal goal : goals) {
        goalRepo.save(goal); // Batch in memory
    }
    // Hibernate flushes all at once - efficient
}

public Integer createGoalAndGetId(Goal goal) {
    goalRepo.saveAndFlush(goal);
    return goal.getId(); // Need ID immediately
}
```

### 63. What is the difference between findById() and getOne()?

Both retrieve entities by ID, but differently:

**findById()** - Immediately hits database. Returns Optional. Returns null if not found.

**getOne()** - Returns lazy proxy. Hits database when you access properties. Throws exception if not found.

**Example:**
```java
// findById() - immediate database hit
Optional<Employee> optEmp = employeeRepo.findById(101);
if(optEmp.isPresent()) {
    Employee emp = optEmp.get();
    System.out.println(emp.getName()); // Already loaded
}

// getOne() - lazy proxy
Employee emp = employeeRepo.getOne(101);
// No database hit yet - emp is a proxy
System.out.println(emp.getId()); // Still no database hit - ID is known
System.out.println(emp.getName()); // NOW database hit happens
// Throws exception if ID 101 doesn't exist
```

**When to use findById():**
- When you're not sure entity exists
- When you need to check existence
- Most common use case

**When to use getOne():**
- When you're certain entity exists
- When setting up relationships without loading full entity
- Performance optimization

**Example with relationships:**
```java
// Bad - loads entire employee just to set reference
Employee emp = employeeRepo.findById(101).get();
review.setEmployee(emp);

// Good - creates proxy without database hit
Employee emp = employeeRepo.getOne(101);
review.setEmployee(emp); // Just stores reference
reviewRepo.save(review);
```

**In PerformanceTrack:** Use findById() for most cases. Use getOne() when creating reviews/goals and just need to reference an employee without loading their full data.

### 64. What is @Embedded and @Embeddable?

These annotations allow you to embed one class inside another without creating a separate table.

**Use case:** Address, ContactInfo, AuditInfo - common fields grouped together but not separate entities.

**Example:**
```java
// Embeddable class - reusable component
@Embeddable
public class Address {
    private String street;
    private String city;
    private String state;
    private String zipCode;
    // getters, setters
}

// Entity using embedded class
@Entity
public class Employee {
    @Id
    private Integer id;
    private String name;
    
    @Embedded
    private Address address; // Embedded, not separate table
}
```

**Database table:**
```
employees table:
id | name | street | city | state | zip_code
```

All address fields are columns in employees table, not a separate address table.

**Customizing column names:**
```java
@Embedded
@AttributeOverrides({
    @AttributeOverride(name = "street", column = @Column(name = "home_street")),
    @AttributeOverride(name = "city", column = @Column(name = "home_city"))
})
private Address homeAddress;

@Embedded
@AttributeOverrides({
    @AttributeOverride(name = "street", column = @Column(name = "office_street")),
    @AttributeOverride(name = "city", column = @Column(name = "office_city"))
})
private Address officeAddress;
```

**In PerformanceTrack:**
```java
@Embeddable
public class AuditInfo {
    private LocalDateTime createdDate;
    private String createdBy;
    private LocalDateTime modifiedDate;
    private String modifiedBy;
}

@Entity
public class Review {
    @Id
    private Integer id;
    private Integer score;
    
    @Embedded
    private AuditInfo auditInfo; // Reuse audit fields across entities
}

@Entity
public class Goal {
    @Id
    private Integer id;
    private String title;
    
    @Embedded
    private AuditInfo auditInfo; // Same audit fields
}
```

### 65. What is @Enumerated and how does it work?

`@Enumerated` maps Java enums to database columns.

**Two strategies:**

**ORDINAL (Default)** - Stores enum position (0, 1, 2...). Dangerous!

**STRING** - Stores enum name. Safe and readable.

**Example:**
```java
public enum GoalStatus {
    DRAFT,      // Ordinal: 0
    SUBMITTED,  // Ordinal: 1
    APPROVED,   // Ordinal: 2
    IN_PROGRESS,// Ordinal: 3
    COMPLETED   // Ordinal: 4
}

@Entity
public class Goal {
    @Id
    private Integer id;
    
    // Bad - stores 0, 1, 2, 3, 4
    @Enumerated(EnumType.ORDINAL)
    private GoalStatus status;
    
    // Good - stores "DRAFT", "SUBMITTED", etc.
    @Enumerated(EnumType.STRING)
    private GoalStatus status;
}
```

**Why ORDINAL is dangerous:**
```java
// Original enum
public enum GoalStatus {
    DRAFT,      // 0
    SUBMITTED,  // 1
    APPROVED    // 2
}

// Later you add new status at beginning
public enum GoalStatus {
    PENDING,    // 0 - NEW!
    DRAFT,      // 1 - changed from 0!
    SUBMITTED,  // 2 - changed from 1!
    APPROVED    // 3 - changed from 2!
}
// All existing database values now mean different things!
```

**With STRING:** No problem - database has "DRAFT", "SUBMITTED" - still correct.

**In PerformanceTrack:**
```java
public enum ReviewStatus {
    PENDING, IN_PROGRESS, COMPLETED, PUBLISHED
}

@Entity
public class Review {
    @Enumerated(EnumType.STRING)
    @Column(length = 20)
    private ReviewStatus status;
}
```

**Always use EnumType.STRING!**

### 66. What is @Temporal and when do we use it?

`@Temporal` was used to map Date/Time fields in older Java (before Java 8).

**Three types:**
- `TemporalType.DATE` - Only date (2024-01-15)
- `TemporalType.TIME` - Only time (14:30:00)
- `TemporalType.TIMESTAMP` - Date and time (2024-01-15 14:30:00)

**Example (Old way with java.util.Date):**
```java
@Entity
public class Review {
    @Temporal(TemporalType.DATE)
    private Date reviewDate; // Only date stored
    
    @Temporal(TemporalType.TIME)
    private Date reviewTime; // Only time stored
    
    @Temporal(TemporalType.TIMESTAMP)
    private Date submittedAt; // Date and time stored
}
```

**Modern way (Java 8+ - NO @Temporal needed):**
```java
@Entity
public class Review {
    private LocalDate reviewDate;        // Automatically mapped as DATE
    private LocalTime reviewTime;        // Automatically mapped as TIME
    private LocalDateTime submittedAt;   // Automatically mapped as TIMESTAMP
}
```

**In PerformanceTrack:** If using Java 8+, use LocalDate/LocalDateTime - cleaner and no @Temporal needed. Only use @Temporal if stuck with old java.util.Date.

### 67. What is @Version and Optimistic Locking?

`@Version` implements optimistic locking to prevent lost updates when multiple users edit same data.

**The Problem:**
```
User A reads Employee (version 1, salary 50000)
User B reads Employee (version 1, salary 50000)
User A updates salary to 55000, saves (version becomes 2)
User B updates salary to 60000, saves
Result: User A's update is lost!
```

**Solution with @Version:**
```java
@Entity
public class Employee {
    @Id
    private Integer id;
    private String name;
    private Double salary;
    
    @Version
    private Integer version; // Hibernate manages this automatically
}
```

**How it works:**
```
User A reads Employee (version 1, salary 50000)
User B reads Employee (version 1, salary 50000)

User A updates salary to 55000, saves
Hibernate: UPDATE employees SET salary=55000, version=2 WHERE id=101 AND version=1
Success! Version becomes 2

User B updates salary to 60000, tries to save
Hibernate: UPDATE employees SET salary=60000, version=2 WHERE id=101 AND version=1
Fails! Version is now 2, not 1
Throws OptimisticLockException
```

User B gets error, reloads fresh data, applies their change on top of User A's change.

**In PerformanceTrack:**
```java
@Entity
public class Review {
    @Id
    private Integer id;
    
    @Version
    private Integer version;
    
    private Integer score;
}

// Service layer
@Transactional
public void updateReview(Integer reviewId, Integer newScore) {
    Review review = reviewRepo.findById(reviewId).get();
    review.setScore(newScore);
    reviewRepo.save(review);
    // If another user modified this review meanwhile, throws OptimisticLockException
}
```

Prevents managers from overwriting each other's review edits!

### 68. What is the difference between persist() and merge() in JPA?

**persist()** - Makes transient object persistent. Only for NEW objects.

**merge()** - Copies detached object's state to managed object. For EXISTING objects.

**Example:**
```java
@Service
public class EmployeeService {
    
    @PersistenceContext
    private EntityManager em;
    
    @Transactional
    public void demonstratePersistVsMerge() {
        
        // persist() - for NEW objects
        Employee newEmp = new Employee("John"); // Transient
        em.persist(newEmp); // Now persistent, tracked by EntityManager
        newEmp.setName("John Doe"); // Auto-updates database
        
        // merge() - for DETACHED objects
        Employee detachedEmp = new Employee(101, "Jane"); // Has ID, but detached
        Employee managedEmp = em.merge(detachedEmp); 
        // detachedEmp is still detached
        // managedEmp is persistent and tracked
        
        detachedEmp.setName("Jane Doe"); // Won't update database
        managedEmp.setName("Jane Smith"); // Will update database
    }
}
```

**Key differences:**

**persist():**
- Object must be new (no ID)
- Makes the SAME object persistent
- Throws exception if object exists
- More efficient for new objects

**merge():**
- Object can be new or existing
- Returns a DIFFERENT managed object
- Updates if exists, inserts if new
- Safer for detached objects

**In PerformanceTrack:**
```java
// Creating new review - use persist()
Review newReview = new Review();
newReview.setScore(85);
em.persist(newReview);

// Updating review from web form (detached) - use merge()
Review managedReview = em.merge(formReview);
```

Shall I continue with more advanced topics?