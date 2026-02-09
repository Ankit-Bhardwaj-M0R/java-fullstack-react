# Complete Spring Ecosystem Architecture

Let me give you the **FULL ARCHITECTURAL PICTURE** of how everything connects - from top to bottom!

---

## 🏗️ **COMPLETE LAYERED ARCHITECTURE**

```
┌──────────────────────────────────────────────────────────────────┐
│                         CLIENT LAYER                              │
│  (Web Browser, Mobile App, Postman, Other Microservices)         │
└────────────────────────────┬─────────────────────────────────────┘
                             │ HTTP/HTTPS Request
                             ↓
┌──────────────────────────────────────────────────────────────────┐
│                    SPRING SECURITY LAYER                          │
│  ┌────────────────────────────────────────────────────────┐      │
│  │  Security Filter Chain                                  │      │
│  │  • JWT Authentication Filter                            │      │
│  │  • CORS Filter                                          │      │
│  │  • Authorization Filter                                 │      │
│  │  • Session Management                                   │      │
│  └────────────────────────────────────────────────────────┘      │
│         │ If authenticated & authorized                           │
└─────────┴──────────────────────────────────────────────────────┘
          ↓
┌──────────────────────────────────────────────────────────────────┐
│                    PRESENTATION LAYER                             │
│                    (Spring REST / Spring MVC)                     │
│  ┌────────────────────────────────────────────────────────┐      │
│  │  REST Controllers (@RestController)                     │      │
│  │  • EmployeeController                                   │      │
│  │  • GoalController                                       │      │
│  │  • ReviewController                                     │      │
│  │  • AuthController                                       │      │
│  │                                                          │      │
│  │  Annotations:                                           │      │
│  │  @GetMapping, @PostMapping, @PutMapping, @DeleteMapping│      │
│  │  @RequestBody, @PathVariable, @RequestParam            │      │
│  └────────────────────────────────────────────────────────┘      │
│         │ Calls                                                    │
└─────────┴──────────────────────────────────────────────────────┘
          ↓
┌──────────────────────────────────────────────────────────────────┐
│                    BUSINESS LOGIC LAYER                           │
│                    (Service Layer)                                │
│  ┌────────────────────────────────────────────────────────┐      │
│  │  Service Classes (@Service)                             │      │
│  │  • EmployeeService                                      │      │
│  │  • GoalService                                          │      │
│  │  • ReviewService                                        │      │
│  │  • NotificationService                                  │      │
│  │  • AnalyticsService                                     │      │
│  │                                                          │      │
│  │  Business Rules, Validations, Workflows                │      │
│  └────────────────────────────────────────────────────────┘      │
│         │ Calls                                                    │
└─────────┴──────────────────────────────────────────────────────┘
          ↓
┌──────────────────────────────────────────────────────────────────┐
│                    DATA ACCESS LAYER                              │
│                    (Spring Data JPA)                              │
│  ┌────────────────────────────────────────────────────────┐      │
│  │  Repository Interfaces (@Repository)                    │      │
│  │  • EmployeeRepository extends JpaRepository             │      │
│  │  • GoalRepository extends JpaRepository                 │      │
│  │  • ReviewRepository extends JpaRepository               │      │
│  │                                                          │      │
│  │  Methods: findAll(), findById(), save(), delete()       │      │
│  │  Custom Queries: @Query, findByNameContaining()        │      │
│  └────────────────────────────────────────────────────────┘      │
│         │ Spring Data JPA implements automatically                │
└─────────┴──────────────────────────────────────────────────────┘
          ↓
┌──────────────────────────────────────────────────────────────────┐
│                    ORM LAYER (HIBERNATE)                          │
│  ┌────────────────────────────────────────────────────────┐      │
│  │  Entity Manager                                         │      │
│  │  • Session Management                                   │      │
│  │  • First Level Cache                                    │      │
│  │  • Second Level Cache (Optional)                        │      │
│  │  • Lazy/Eager Loading                                   │      │
│  │  • Dirty Checking                                       │      │
│  │  • Transaction Management                               │      │
│  │                                                          │      │
│  │  Entity Classes (@Entity)                               │      │
│  │  • Employee (@Id, @Column, @OneToMany, @ManyToOne)     │      │
│  │  • Goal, Review, Department                            │      │
│  └────────────────────────────────────────────────────────┘      │
│         │ Converts Objects ↔ SQL                                  │
└─────────┴──────────────────────────────────────────────────────┘
          ↓
┌──────────────────────────────────────────────────────────────────┐
│                    JDBC LAYER                                     │
│  ┌────────────────────────────────────────────────────────┐      │
│  │  JDBC Connection Pool (HikariCP)                        │      │
│  │  • Manages database connections                         │      │
│  │  • Connection pooling (reuse connections)               │      │
│  │  • PreparedStatement execution                          │      │
│  │  • ResultSet handling                                   │      │
│  └────────────────────────────────────────────────────────┘      │
│         │ Direct SQL execution                                    │
└─────────┴──────────────────────────────────────────────────────┘
          ↓
┌──────────────────────────────────────────────────────────────────┐
│                    DATABASE LAYER                                 │
│                    (MySQL / PostgreSQL)                           │
│  ┌────────────────────────────────────────────────────────┐      │
│  │  Tables: employees, goals, reviews, departments         │      │
│  │  Indexes, Foreign Keys, Constraints                     │      │
│  └────────────────────────────────────────────────────────┘      │
└──────────────────────────────────────────────────────────────────┘
```

---

## 🎯 **SPRING BOOT ARCHITECTURE**

```
                    ┌─────────────────────────┐
                    │   Spring Boot App       │
                    │  @SpringBootApplication │
                    └───────────┬─────────────┘
                                │
                ┌───────────────┼───────────────┐
                │               │               │
                ↓               ↓               ↓
    ┌──────────────────┐ ┌──────────┐ ┌─────────────────┐
    │ Auto-Configuration│ │Embedded  │ │  Starter        │
    │                   │ │Tomcat    │ │  Dependencies   │
    │ • DataSource      │ │Server    │ │                 │
    │ • JPA             │ │          │ │ • spring-boot-  │
    │ • Security        │ │Port:8080 │ │   starter-web   │
    │ • REST            │ │          │ │ • starter-data- │
    │ • Jackson (JSON)  │ │          │ │   jpa           │
    └──────────────────┘ └──────────┘ │ • starter-      │
                                       │   security      │
                                       └─────────────────┘
```

---

## 🔐 **SPRING SECURITY ARCHITECTURE**

```
HTTP Request
     ↓
┌─────────────────────────────────────────────────┐
│         Security Filter Chain                   │
│                                                  │
│  1. CorsFilter                                  │
│     ↓                                            │
│  2. JwtAuthenticationFilter (Custom)            │
│     • Extracts JWT from Authorization header    │
│     • Validates token                           │
│     • Sets SecurityContext                      │
│     ↓                                            │
│  3. UsernamePasswordAuthenticationFilter        │
│     ↓                                            │
│  4. AuthorizationFilter                         │
│     • Checks user roles/permissions             │
│     • @PreAuthorize("hasRole('MANAGER')")      │
│     ↓                                            │
│  5. ExceptionTranslationFilter                  │
│     ↓                                            │
│  6. FilterSecurityInterceptor                   │
└─────────────────────────────────────────────────┘
     ↓
If authorized → Controller
If not → 401/403 Error

┌─────────────────────────────────────────────────┐
│    Authentication Flow                          │
│                                                  │
│  POST /api/auth/login                           │
│    ↓                                             │
│  AuthController                                 │
│    ↓                                             │
│  AuthenticationManager                          │
│    ↓                                             │
│  UserDetailsService (loadUserByUsername)        │
│    ↓                                             │
│  Database (fetch user)                          │
│    ↓                                             │
│  Password Encoder (BCrypt compare)              │
│    ↓                                             │
│  Generate JWT Token                             │
│    ↓                                             │
│  Return token to client                         │
└─────────────────────────────────────────────────┘
```

---

## 🔄 **SPRING DATA JPA & HIBERNATE ARCHITECTURE**

```
┌─────────────────────────────────────────────────────────┐
│              JpaRepository Interface                     │
│  (You write this)                                        │
│                                                          │
│  public interface EmployeeRepository                     │
│         extends JpaRepository<Employee, Long> {          │
│                                                          │
│    List<Employee> findByDepartmentId(Long deptId);      │
│    List<Employee> findByNameContaining(String name);    │
│                                                          │
│    @Query("SELECT e FROM Employee e WHERE ...")         │
│    List<Employee> customQuery();                        │
│  }                                                       │
└────────────────────────┬────────────────────────────────┘
                         │
                         ↓
┌─────────────────────────────────────────────────────────┐
│         Spring Data JPA Implementation                   │
│         (Auto-generated by Spring)                       │
│                                                          │
│  • Implements all CRUD methods                          │
│  • Generates SQL from method names                      │
│  • Handles pagination, sorting                          │
│  • Manages transactions                                 │
└────────────────────────┬────────────────────────────────┘
                         │
                         ↓
┌─────────────────────────────────────────────────────────┐
│              Hibernate (JPA Provider)                    │
│                                                          │
│  ┌──────────────────────────────────────────────┐      │
│  │  Persistence Context (EntityManager)         │      │
│  │  • Manages entity lifecycle                  │      │
│  │  • Tracks changes (dirty checking)           │      │
│  │                                               │      │
│  │  Entity States:                              │      │
│  │  [Transient] → [Persistent] → [Detached]    │      │
│  │                     ↓                         │      │
│  │                 [Removed]                     │      │
│  └──────────────────────────────────────────────┘      │
│                                                          │
│  ┌──────────────────────────────────────────────┐      │
│  │  Session Factory                              │      │
│  │  • Creates sessions                           │      │
│  │  • Manages connections                        │      │
│  └──────────────────────────────────────────────┘      │
│                                                          │
│  ┌──────────────────────────────────────────────┐      │
│  │  HQL / JPQL Query Engine                     │      │
│  │  • Converts object queries to SQL             │      │
│  └──────────────────────────────────────────────┘      │
│                                                          │
│  ┌──────────────────────────────────────────────┐      │
│  │  Caching                                      │      │
│  │  • First Level Cache (Session)                │      │
│  │  • Second Level Cache (SessionFactory)        │      │
│  │  • Query Cache                                │      │
│  └──────────────────────────────────────────────┘      │
└────────────────────────┬────────────────────────────────┘
                         │
                         ↓
┌─────────────────────────────────────────────────────────┐
│                    JDBC Layer                            │
│                                                          │
│  • Executes SQL statements                              │
│  • Manages database connections                         │
│  • Returns ResultSet                                    │
└────────────────────────┬────────────────────────────────┘
                         │
                         ↓
                    DATABASE
```

---

## 🌐 **SPRING REST ARCHITECTURE**

```
HTTP Request: GET /api/employees/123
     ↓
┌─────────────────────────────────────────────────┐
│         DispatcherServlet                       │
│         (Front Controller)                      │
│                                                  │
│  • Receives all HTTP requests                   │
│  • Routes to appropriate controller             │
└──────────────────┬──────────────────────────────┘
                   │
                   ↓
┌─────────────────────────────────────────────────┐
│         HandlerMapping                          │
│                                                  │
│  • Maps URL to Controller method                │
│  • /api/employees/123 → getEmployee(123)       │
└──────────────────┬──────────────────────────────┘
                   │
                   ↓
┌─────────────────────────────────────────────────┐
│         Controller Method                       │
│                                                  │
│  @GetMapping("/api/employees/{id}")             │
│  public ResponseEntity<Employee>                │
│       getEmployee(@PathVariable Long id) {      │
│     Employee emp = service.findById(id);        │
│     return ResponseEntity.ok(emp);              │
│  }                                              │
└──────────────────┬──────────────────────────────┘
                   │
                   ↓
┌─────────────────────────────────────────────────┐
│         HttpMessageConverter                    │
│         (Jackson for JSON)                      │
│                                                  │
│  • Converts Java Object → JSON                  │
│  • Converts JSON → Java Object                  │
│                                                  │
│  Employee object → {"id":123, "name":"Kabir"}  │
└──────────────────┬──────────────────────────────┘
                   │
                   ↓
            HTTP Response (JSON)
```

---

## 🏢 **COMPLETE REQUEST FLOW IN YOUR PERFORMANCETRACK APP**

```
Mobile App: GET /api/employees/123/goals
     ↓
┌──────────────────────────────────────┐
│  1. Embedded Tomcat receives request │
└──────────────┬───────────────────────┘
               ↓
┌──────────────────────────────────────┐
│  2. CORS Filter (allows origin)      │
└──────────────┬───────────────────────┘
               ↓
┌──────────────────────────────────────┐
│  3. JWT Authentication Filter        │
│     • Extracts: "Bearer eyJhbGc..." │
│     • Validates JWT signature        │
│     • Checks expiration              │
│     • Loads user details             │
│     • Sets SecurityContext           │
└──────────────┬───────────────────────┘
               ↓
┌──────────────────────────────────────┐
│  4. Authorization Filter             │
│     • Checks user has EMPLOYEE role  │
│     • Verifies access to resource    │
└──────────────┬───────────────────────┘
               ↓
┌──────────────────────────────────────┐
│  5. DispatcherServlet                │
│     • Routes to GoalController       │
└──────────────┬───────────────────────┘
               ↓
┌──────────────────────────────────────┐
│  6. GoalController                   │
│     @GetMapping("/api/employees/     │
│                 {id}/goals")         │
│     getEmployeeGoals(123)            │
└──────────────┬───────────────────────┘
               ↓
┌──────────────────────────────────────┐
│  7. GoalService                      │
│     • Business logic                 │
│     • Validates employee exists      │
│     • Checks permissions             │
│     • Calls repository               │
└──────────────┬───────────────────────┘
               ↓
┌──────────────────────────────────────┐
│  8. GoalRepository                   │
│     findByEmployeeId(123)            │
└──────────────┬───────────────────────┘
               ↓
┌──────────────────────────────────────┐
│  9. Spring Data JPA                  │
│     • Generates query                │
│     • Calls Hibernate                │
└──────────────┬───────────────────────┘
               ↓
┌──────────────────────────────────────┐
│  10. Hibernate                       │
│     • Checks First Level Cache       │
│     • If not found, generates SQL:   │
│       SELECT * FROM goals            │
│       WHERE employee_id = 123        │
│     • Executes via JDBC              │
└──────────────┬───────────────────────┘
               ↓
┌──────────────────────────────────────┐
│  11. HikariCP Connection Pool        │
│     • Gets connection from pool      │
│     • Executes SQL on MySQL          │
└──────────────┬───────────────────────┘
               ↓
┌──────────────────────────────────────┐
│  12. MySQL Database                  │
│     • Executes query                 │
│     • Returns ResultSet              │
└──────────────┬───────────────────────┘
               ↓
┌──────────────────────────────────────┐
│  13. Hibernate                       │
│     • Maps ResultSet → Goal objects  │
│     • Stores in cache                │
│     • Returns List<Goal>             │
└──────────────┬───────────────────────┘
               ↓
┌──────────────────────────────────────┐
│  14. GoalService                     │
│     • Applies business logic         │
│     • Returns to controller          │
└──────────────┬───────────────────────┘
               ↓
┌──────────────────────────────────────┐
│  15. GoalController                  │
│     • Wraps in ResponseEntity        │
│     • Returns List<Goal>             │
└──────────────┬───────────────────────┘
               ↓
┌──────────────────────────────────────┐
│  16. Jackson (JSON Converter)        │
│     • Converts List<Goal> → JSON     │
│     [{"id":1,"title":"Learn Java"}]  │
└──────────────┬───────────────────────┘
               ↓
┌──────────────────────────────────────┐
│  17. HTTP Response                   │
│     Status: 200 OK                   │
│     Body: JSON array of goals        │
└──────────────┬───────────────────────┘
               ↓
          Mobile App receives response
```

---

## 🗂️ **SPRING BOOT PROJECT STRUCTURE**

```
performance-track/
│
├── src/main/java/com/cognizant/performancetrack/
│   │
│   ├── PerformanceTrackApplication.java  ← Main class
│   │
│   ├── config/                            ← Configuration
│   │   ├── SecurityConfig.java
│   │   ├── JwtConfig.java
│   │   └── DatabaseConfig.java
│   │
│   ├── controller/                        ← REST Layer
│   │   ├── EmployeeController.java
│   │   ├── GoalController.java
│   │   ├── ReviewController.java
│   │   └── AuthController.java
│   │
│   ├── service/                           ← Business Logic
│   │   ├── EmployeeService.java
│   │   ├── GoalService.java
│   │   ├── ReviewService.java
│   │   └── NotificationService.java
│   │
│   ├── repository/                        ← Data Access
│   │   ├── EmployeeRepository.java
│   │   ├── GoalRepository.java
│   │   └── ReviewRepository.java
│   │
│   ├── entity/                            ← Database Models
│   │   ├── Employee.java
│   │   ├── Goal.java
│   │   ├── Review.java
│   │   └── Department.java
│   │
│   ├── dto/                               ← Data Transfer Objects
│   │   ├── EmployeeDTO.java
│   │   ├── GoalDTO.java
│   │   └── LoginRequest.java
│   │
│   ├── security/                          ← Security Components
│   │   ├── JwtAuthenticationFilter.java
│   │   ├── JwtTokenProvider.java
│   │   └── CustomUserDetailsService.java
│   │
│   ├── exception/                         ← Exception Handling
│   │   ├── GlobalExceptionHandler.java
│   │   ├── ResourceNotFoundException.java
│   │   └── UnauthorizedException.java
│   │
│   └── util/                              ← Utility Classes
│       ├── DateUtil.java
│       └── ResponseUtil.java
│
├── src/main/resources/
│   ├── application.properties             ← Configuration
│   ├── application-dev.properties
│   ├── application-prod.properties
│   └── data.sql                           ← Initial data
│
├── src/test/java/                         ← Tests
│   ├── controller/
│   ├── service/
│   └── repository/
│
└── pom.xml                                ← Dependencies
```

---

## 📦 **SPRING BOOT DEPENDENCIES (pom.xml)**

```xml
<!-- Parent POM -->
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>3.2.0</version>
</parent>

<dependencies>
    <!-- Spring Boot Web (REST APIs + Tomcat) -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    
    <!-- Spring Data JPA + Hibernate -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-jpa</artifactId>
    </dependency>
    
    <!-- Spring Security + JWT -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-security</artifactId>
    </dependency>
    
    <!-- MySQL JDBC Driver -->
    <dependency>
        <groupId>com.mysql</groupId>
        <artifactId>mysql-connector-j</artifactId>
    </dependency>
    
    <!-- JWT Library -->
    <dependency>
        <groupId>io.jsonwebtoken</groupId>
        <artifactId>jjwt-api</artifactId>
    </dependency>
</dependencies>
```

---

## 🔄 **DATA FLOW: Save New Goal**

```
POST /api/goals
Body: {"title":"Learn Spring","employeeId":123}
     ↓
[Security Filters] → Authenticate & Authorize
     ↓
GoalController.createGoal(goalDTO)
     ↓
GoalService.createGoal(goalDTO)
  • Validates data
  • Checks employee exists
  • Sets default values (status=PENDING, createdDate=now)
  • Calls repository
     ↓
GoalRepository.save(goal)
     ↓
Spring Data JPA
  • Detects new entity (id = null)
  • Calls Hibernate persist()
     ↓
Hibernate
  • Adds to Persistence Context
  • Marks as Persistent state
  • Generates SQL: INSERT INTO goals (title, employee_id, status, created_date)
                  VALUES ('Learn Spring', 123, 'PENDING', '2026-02-09')
     ↓
JDBC Connection Pool (HikariCP)
  • Gets connection
  • Executes INSERT statement
  • Returns generated ID
     ↓
MySQL Database
  • Inserts record
  • Returns auto-generated goal_id = 456
     ↓
Hibernate
  • Sets goal.id = 456
  • Returns managed Goal object
     ↓
GoalService
  • Additional processing (send notification?)
  • Returns Goal
     ↓
GoalController
  • Returns ResponseEntity with status 201 CREATED
     ↓
Jackson converts Goal → JSON
     ↓
HTTP Response: 201 Created
Body: {"id":456,"title":"Learn Spring","status":"PENDING",...}
```

---

## 🎓 **KEY CONCEPTS SUMMARY**

### **Spring Framework**
- Core container with Dependency Injection
- Foundation for all Spring projects

### **Spring Boot**
- Simplifies Spring setup with auto-configuration
- Embedded server (no separate Tomcat installation)
- Starter dependencies (bundles commonly used libraries)

### **Spring REST**
- Creates RESTful web services
- Handles HTTP requests/responses
- Automatic JSON conversion

### **JDBC**
- Low-level database access
- Manual SQL writing
- Direct connection management

### **Hibernate**
- ORM framework
- Maps Java objects to database tables
- Manages entity lifecycle and caching

### **Spring Data JPA**
- Builds on Hibernate
- Repository pattern with no implementation needed
- Query generation from method names

### **Spring Security**
- Authentication (who you are)
- Authorization (what you can do)
- JWT token-based security for REST APIs

---

This is the **COMPLETE PICTURE** of how everything fits together! Each layer has a specific responsibility and they all work together seamlessly.



Does this give you the full architectural understanding you need? 🚀
