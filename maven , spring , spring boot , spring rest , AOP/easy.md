# Complete Interview Questions Guide: Maven, Spring, Spring Boot, Spring REST, AOP

I'll provide you with an extensive list of interview questions with conversational answers. Since this is a huge topic, I'll organize them clearly and you can ask me to continue whenever needed.

---

## **MAVEN Interview Questions**

### **Q1: What is Maven and why do we use it?**

Maven is a build automation tool that helps us manage Java projects easily. Think of it like a project manager for your code.

Imagine you're building the PerformanceTrack system. Without Maven, you'd need to manually download Spring Boot libraries, Hibernate JARs, MySQL connectors - maybe 50+ files! Then you'd have to manage versions, make sure they're compatible, and compile everything yourself.

With Maven, you just write a simple `pom.xml` file saying "I need Spring Boot and MySQL connector," and Maven downloads everything automatically, compiles your code, runs tests, and creates a deployable JAR file. It's like ordering a complete meal instead of buying individual ingredients.

---

### **Q2: What is a POM file?**

POM stands for Project Object Model. It's an XML file named `pom.xml` that contains all the information about your project.

Think of it as a recipe book for your project. In PerformanceTrack, your `pom.xml` tells Maven:
- Project name and version
- What libraries you need (Spring Boot, MySQL driver)
- Which Java version to use
- How to build the project

For example, when you add `<dependency>` for Spring Web, Maven knows to download Spring Web JAR and all its related libraries automatically.

---

### **Q3: What is the difference between groupId, artifactId, and version in Maven?**

These three things uniquely identify any library in the Maven world.

Let's use a real example from your project:
```xml
<groupId>org.springframework.boot</groupId>
<artifactId>spring-boot-starter-web</artifactId>
<version>2.7.0</version>
```

- **groupId**: The company or organization name (like org.springframework.boot)
- **artifactId**: The actual library name (like spring-boot-starter-web)
- **version**: Which version you want (like 2.7.0)

Think of it like an address: groupId is the city, artifactId is the street name, and version is the house number. Together, they point to exactly one library in Maven's central repository.

---

### **Q4: What is Maven Central Repository?**

It's a huge online library where all Java libraries are stored. When you add a dependency in `pom.xml`, Maven downloads it from here.

Imagine you're in PerformanceTrack and add MySQL dependency. Maven connects to Maven Central (a website), finds the MySQL connector JAR file, downloads it to your computer, and adds it to your project. You never have to visit websites manually to download libraries.

It's like Amazon for Java libraries - everything is organized and available to download automatically.

---

### **Q5: What is the difference between dependency and plugin in Maven?**

**Dependencies** are libraries your code needs to run. **Plugins** are tools that help build your project.

In PerformanceTrack:
- **Dependency example**: Spring Boot, MySQL driver - your code uses these to run the application
- **Plugin example**: Maven compiler plugin - this compiles your Java code into class files

Think of dependencies as ingredients for your dish (you need them in the final product), and plugins as kitchen tools (you need them to prepare the dish, but they don't go into the final meal).

---

### **Q6: What are Maven build lifecycle phases?**

Maven follows a sequence of steps to build your project. The main phases are:

1. **validate** - Check if project is correct
2. **compile** - Compile your Java code
3. **test** - Run unit tests
4. **package** - Create JAR/WAR file
5. **verify** - Run checks on the package
6. **install** - Put package in local repository
7. **deploy** - Copy to remote repository

When you run `mvn package` for PerformanceTrack, Maven automatically runs validate, compile, test, and then package. You don't need to run each phase separately. It's like a assembly line - each step happens in order.

---

### **Q7: What is the difference between mvn clean, mvn install, and mvn package?**

These are different Maven commands for building your project:

**mvn clean**: Deletes the `target` folder (where compiled files are stored). It's like clearing your workspace before starting fresh.

**mvn package**: Compiles code, runs tests, and creates a JAR file in the target folder. For PerformanceTrack, this creates `performancetrack.jar` that you can run.

**mvn install**: Does everything package does, PLUS copies the JAR to your local Maven repository (a folder on your computer). This is useful if you have multiple projects that depend on each other.

In your daily work, you'll mostly use `mvn clean package` to get a fresh build.

---

### **Q8: What is SNAPSHOT version in Maven?**

SNAPSHOT means "work in progress" version. It's used during development.

In PerformanceTrack, you might use version `1.0-SNAPSHOT` while building the project. Every time you build, Maven can download the latest SNAPSHOT version. Once the project is stable and released, you change it to `1.0` (without SNAPSHOT).

Think of SNAPSHOT like a Google Doc that's still being edited - the content changes frequently. A regular version (like 1.0) is like a published PDF - it's fixed and won't change.

---

### **Q9: What is the Maven local repository?**

It's a folder on your computer (usually at `C:\Users\YourName\.m2\repository`) where Maven stores all downloaded libraries.

When you add Spring Boot dependency, Maven first checks your local repository. If it's there, it uses it. If not, it downloads from Maven Central and saves it locally.

This saves time - if you create a new Spring Boot project tomorrow, Maven won't download Spring Boot again because it's already in your local repository. It's like keeping a personal library instead of going to the bookstore every time.

---

### **Q10: What are Maven profiles?**

Profiles let you have different configurations for different environments.

In PerformanceTrack, you might have:
- **dev profile**: Uses local MySQL database on localhost
- **production profile**: Uses company's production database server

You define profiles in `pom.xml` and activate them using `mvn package -P dev` or `mvn package -P production`. This way, you don't need separate code for different environments - same code, different configurations.

---

### **Q11: What is the difference between scope compile, provided, runtime, and test in Maven dependencies?**

Scope tells Maven when a dependency is needed:

**compile** (default): Needed everywhere - compiling, testing, and running. Example: Spring Boot in PerformanceTrack.

**provided**: Needed for compiling and testing, but NOT in the final JAR because the server provides it. Example: Servlet API (Tomcat already has it).

**runtime**: NOT needed for compiling, but needed to run. Example: MySQL driver (you don't write MySQL-specific code, but you need the driver at runtime).

**test**: Only needed for testing. Example: JUnit library.

For PerformanceTrack, most dependencies will be compile scope. This keeps things simple.

---

### **Q12: What is a parent POM?**

A parent POM is like a template that other POMs can inherit from.

In Spring Boot projects like PerformanceTrack, you'll see:
```xml
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>2.7.0</version>
</parent>
```

This parent POM provides default configurations - Java version, plugin versions, dependency versions. You don't have to specify versions for Spring dependencies because the parent POM manages them. It's like inheriting good practices from an experienced developer.

---

### **Q13: How do you exclude a transitive dependency in Maven?**

Sometimes a library brings unwanted dependencies. You can exclude them.

Suppose Spring Boot brings an old logging library you don't want:
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
    <exclusions>
        <exclusion>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-logging</artifactId>
        </exclusion>
    </exclusions>
</dependency>
```

This tells Maven "Give me Spring Web, but don't include the logging library." It's like ordering a burger without pickles.

---

### **Q14: What is the Maven compiler plugin used for?**

This plugin compiles your Java source code into bytecode (class files).

In PerformanceTrack, you specify which Java version to use:
```xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-compiler-plugin</artifactId>
    <configuration>
        <source>1.8</source>
        <target>1.8</target>
    </configuration>
</plugin>
```

This tells Maven "Compile my code using Java 8 syntax and create class files compatible with Java 8." Without this, Maven might use a different Java version and cause compatibility issues.

---

### **Q15: How do you create an executable JAR using Maven?**

You use the Maven Shade plugin or Spring Boot Maven plugin.

For PerformanceTrack (Spring Boot project), it's automatic:
```xml
<plugin>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-maven-plugin</artifactId>
</plugin>
```

When you run `mvn package`, this creates a "fat JAR" - a single JAR file containing your code AND all dependencies. You can run it with `java -jar performancetrack.jar`. It's like packing everything into one suitcase instead of carrying multiple bags.

---

## **SPRING FRAMEWORK Interview Questions**

### **Q16: What is the Spring Framework?**

Spring is a framework that makes Java enterprise development easier by providing ready-made solutions for common problems.

Without Spring in PerformanceTrack, you'd have to manually create database connections, manage transactions, handle HTTP requests, create objects, and wire them together - hundreds of lines of repetitive code.

With Spring, you just use annotations like `@Autowired`, `@Service`, `@Transactional`, and Spring handles all the complex stuff. It's like using a washing machine instead of washing clothes by hand - the machine handles the difficult work, you just load the clothes.

---

### **Q17: What is Dependency Injection?**

Dependency Injection means Spring creates objects and provides them to your classes automatically. You don't use `new` keyword.

In PerformanceTrack, your `ReportController` needs `ReportService`:
```java
@RestController
public class ReportController {
    @Autowired
    private ReportService reportService;  // Spring injects this
}
```

Spring creates the `ReportService` object and gives it to `ReportController`. You never write `new ReportService()`. This makes code flexible - if you want to change the implementation tomorrow, you just change one place, not everywhere `new ReportService()` was called.

---

### **Q18: What is Inversion of Control (IoC)?**

IoC means "don't call us, we'll call you." Instead of your code controlling object creation, Spring controls it.

Normally, you write: `ReportService service = new ReportService();` - YOU control when and how it's created.

With Spring IoC: You declare `@Autowired ReportService service;` - SPRING controls when and how it's created.

Think of it like ordering food at a restaurant. Without IoC, you go to the kitchen and cook yourself (you control everything). With IoC, you place an order and the kitchen delivers food to you (Spring controls the creation and delivery).

---

### **Q19: What is the Spring IoC Container?**

The IoC Container is like a factory inside Spring that creates and manages all objects (called beans) in your application.

When PerformanceTrack starts, Spring's IoC Container:
1. Scans for classes with `@Component`, `@Service`, `@Repository`, `@Controller`
2. Creates objects of these classes
3. Injects dependencies using `@Autowired`
4. Manages their lifecycle

The two main containers are **BeanFactory** (basic) and **ApplicationContext** (advanced, used in Spring Boot). You don't directly interact with them - Spring Boot creates ApplicationContext automatically when your application starts.

---

### **Q20: What are Spring Beans?**

Beans are simply objects managed by Spring's IoC Container.

In PerformanceTrack:
- `ReportController` is a bean
- `ReportService` is a bean
- `ReportRepository` is a bean

You mark classes with `@Component`, `@Service`, `@Repository`, or `@Controller`, and Spring treats them as beans - creates them, manages them, and injects them wherever needed. Regular Java objects you create with `new` keyword are NOT beans; Spring doesn't manage them.

---

**Should I continue with more Spring questions? I have many more covering:**
- Bean Scopes
- ApplicationContext vs BeanFactory
- Component Scanning
- Stereotype Annotations
- Bean Lifecycle
- Configuration methods
- And more Spring topics before moving to Spring Boot, REST, and AOP...

Let me know and I'll continue!

## **SPRING FRAMEWORK Interview Questions (Continued)**

### **Q21: What are different bean scopes in Spring?**

Bean scope defines how many instances of a bean Spring creates. There are five scopes, but you'll mainly use two:

**Singleton (default)**: Spring creates only ONE instance for the entire application. Every time you autowire it, you get the same object.

In PerformanceTrack, `ReportService` is singleton by default. If 10 controllers autowire it, all 10 get the same `ReportService` object. This saves memory.

**Prototype**: Spring creates a NEW instance every time you request it. If 10 controllers autowire it, Spring creates 10 different objects.

```java
@Service
@Scope("prototype")
public class ReportService {
    // new instance every time
}
```

Other scopes (request, session, application) are for web applications but rarely used in REST APIs. For PerformanceTrack, stick with singleton - it's efficient and thread-safe when coded properly.

---

### **Q22: What is the difference between ApplicationContext and BeanFactory?**

Both are IoC containers, but ApplicationContext is more powerful.

**BeanFactory**: Basic container. Creates beans lazily (only when you request them). Minimal features.

**ApplicationContext**: Advanced container. Creates beans eagerly (at startup). Has extra features like event handling, internationalization, and automatic bean post-processing.

In PerformanceTrack (Spring Boot), you always use ApplicationContext. Spring Boot creates it automatically. BeanFactory is old-school and rarely used in modern applications.

Think of BeanFactory as a basic phone and ApplicationContext as a smartphone - both make calls, but the smartphone does much more.

---

### **Q23: What is Component Scanning in Spring?**

Component scanning is how Spring finds your beans automatically.

In PerformanceTrack, you have classes in packages like `com.performancetrack.controller`, `com.performancetrack.service`, etc. Spring Boot automatically scans the package where your main class is located and all sub-packages.

```java
@SpringBootApplication  // This enables component scanning
public class PerformanceTrackApplication {
    public static void main(String[] args) {
        SpringApplication.run(PerformanceTrackApplication.class, args);
    }
}
```

Spring finds all classes with `@Component`, `@Service`, `@Repository`, `@Controller` and creates beans. You don't need to register each class manually. It's like Spring walking through your code folders and picking up all the important classes automatically.

---

### **Q24: What is the difference between @Component, @Service, @Repository, and @Controller?**

Technically, they all do the same thing - mark a class as a Spring bean. But they have different meanings:

**@Component**: Generic annotation for any Spring-managed component.

**@Service**: Used for business logic layer. In PerformanceTrack, `ReportService` has `@Service` because it contains calculation logic.

**@Repository**: Used for data access layer. `ReportRepository` has `@Repository` (actually extends JpaRepository, which is already a repository).

**@Controller**: Used for web controllers that return views (HTML pages).

Using the right annotation makes code readable. When someone sees `@Service`, they know "this class has business logic." It's like labeling boxes - all boxes hold things, but labels tell you what's inside.

---

### **Q25: What is @Autowired annotation?**

`@Autowired` tells Spring to inject a dependency automatically.

In PerformanceTrack:
```java
@RestController
public class ReportController {
    @Autowired
    private ReportService reportService;
}
```

Spring sees `@Autowired` and thinks "This controller needs ReportService. Let me find the ReportService bean and inject it here." You never write `new ReportService()`.

You can autowire on fields (like above), constructors, or setter methods. Constructor injection is considered best practice, but field injection is simpler and commonly used in smaller projects.

---

### **Q26: What is Constructor Injection vs Field Injection vs Setter Injection?**

These are three ways to inject dependencies.

**Field Injection** (most common in Spring Boot):
```java
@Autowired
private ReportService reportService;
```
Simple and clean, but harder to test.

**Constructor Injection** (recommended):
```java
private final ReportService reportService;

@Autowired
public ReportController(ReportService reportService) {
    this.reportService = reportService;
}
```
Better for testing and makes dependencies obvious.

**Setter Injection** (rarely used):
```java
private ReportService reportService;

@Autowired
public void setReportService(ReportService reportService) {
    this.reportService = reportService;
}
```

For PerformanceTrack, field injection is fine for learning. In real industry projects, teams prefer constructor injection because it makes code more testable and explicit.

---

### **Q27: What happens if Spring finds multiple beans of the same type?**

Spring gets confused and throws an error.

Suppose you have two implementations of a service:
```java
@Service
public class EmailNotificationService implements NotificationService { }

@Service
public class SmsNotificationService implements NotificationService { }
```

When you autowire:
```java
@Autowired
private NotificationService notificationService;  // Which one??
```

Spring doesn't know which to inject. Solutions:

**Use @Primary**: Mark one as default
```java
@Service
@Primary
public class EmailNotificationService implements NotificationService { }
```

**Use @Qualifier**: Specify which one
```java
@Autowired
@Qualifier("emailNotificationService")
private NotificationService notificationService;
```

---

### **Q28: What is the Spring Bean lifecycle?**

When Spring creates a bean, it goes through several steps:

1. **Instantiate**: Spring creates the object using constructor
2. **Populate properties**: Spring injects dependencies using @Autowired
3. **setBeanName**: If bean implements BeanNameAware, Spring calls it
4. **setBeanFactory**: If bean implements BeanFactoryAware, Spring calls it
5. **Pre-initialization**: @PostConstruct methods run
6. **Initialization**: If bean implements InitializingBean, afterPropertiesSet() runs
7. **Post-initialization**: Bean is ready to use
8. **Destruction**: When application shuts down, @PreDestroy methods run

For PerformanceTrack, you'll mainly use `@PostConstruct` and `@PreDestroy`:

```java
@Service
public class ReportService {
    @PostConstruct
    public void init() {
        System.out.println("ReportService initialized!");
    }
    
    @PreDestroy
    public void cleanup() {
        System.out.println("ReportService shutting down!");
    }
}
```

---

### **Q29: What is @Configuration and @Bean?**

These are used to define beans manually using Java code instead of annotations.

In PerformanceTrack, suppose you want to configure a custom object:
```java
@Configuration
public class AppConfig {
    
    @Bean
    public PasswordEncoder passwordEncoder() {
        return new BCryptPasswordEncoder();
    }
}
```

`@Configuration` marks a class as a source of bean definitions. `@Bean` marks a method that returns a bean. Spring calls this method and manages the returned object as a bean.

This is useful for third-party classes where you can't add `@Component` annotation (because you don't own the code). For your own classes, use `@Service`, `@Repository`, etc.

---

### **Q30: What is the difference between @Component and @Bean?**

**@Component** is a class-level annotation for your own classes:
```java
@Component
public class ReportService { }
```
Spring scans, finds this class, and creates a bean.

**@Bean** is a method-level annotation for manual bean creation:
```java
@Configuration
public class AppConfig {
    @Bean
    public ReportService reportService() {
        return new ReportService();
    }
}
```
You write the creation logic yourself.

Use `@Component` for your own classes. Use `@Bean` for third-party classes or when you need custom initialization logic. In PerformanceTrack, you'll mostly use `@Component` and its specializations (@Service, @Repository, @Controller).

---

### **Q31: What is @Primary annotation?**

When multiple beans of the same type exist, `@Primary` marks one as the default choice.

Example in PerformanceTrack notification system:
```java
@Service
@Primary
public class EmailNotificationService implements NotificationService { }

@Service
public class SmsNotificationService implements NotificationService { }
```

Now when you autowire:
```java
@Autowired
private NotificationService notificationService;  // Gets EmailNotificationService
```

Spring automatically injects the `@Primary` bean. If you specifically want SMS, use `@Qualifier("smsNotificationService")`.

---

### **Q32: What is @Qualifier annotation?**

`@Qualifier` specifies exactly which bean to inject when multiple options exist.

```java
@Service("emailService")
public class EmailNotificationService implements NotificationService { }

@Service("smsService")
public class SmsNotificationService implements NotificationService { }
```

To inject specific one:
```java
@Autowired
@Qualifier("smsService")
private NotificationService notificationService;  // Gets SmsNotificationService
```

Without `@Qualifier`, you'd need `@Primary` or Spring throws an error. This gives you precise control over which bean gets injected.

---

### **Q33: What is @Value annotation?**

`@Value` injects values from properties files into your beans.

In PerformanceTrack, you have `application.properties`:
```properties
app.report.maxRecords=1000
```

In your service:
```java
@Service
public class ReportService {
    @Value("${app.report.maxRecords}")
    private int maxRecords;  // Gets value 1000
}
```

Spring reads the property file and injects the value. This is great for configuration - you can change values without recompiling code. In production, you might change `maxRecords` to 5000, and just restart the application.

---

### **Q34: What is @PropertySource annotation?**

`@PropertySource` tells Spring to load a custom properties file.

By default, Spring Boot loads `application.properties`. But you might have additional files:
```java
@Configuration
@PropertySource("classpath:custom-config.properties")
public class AppConfig {
}
```

Now Spring loads values from `custom-config.properties` too, and you can use `@Value` to inject them. This is useful for organizing configurations - database properties in one file, email settings in another, etc.

---

### **Q35: What is Spring Expression Language (SpEL)?**

SpEL is a powerful expression language for querying and manipulating objects at runtime.

You can use it with `@Value`:
```java
@Value("#{systemProperties['user.home']}")
private String userHome;

@Value("#{reportService.getMaxRecords()}")
private int maxRecords;

@Value("#{10 * 20}")
private int result;  // 200
```

In PerformanceTrack, you might rarely use SpEL. It's powerful but can make code complex. Simple `@Value("${property.name}")` is usually enough for configuration values.

---

## **SPRING BOOT Interview Questions**

### **Q36: What is Spring Boot?**

Spring Boot is a framework built on top of Spring that makes development faster by providing automatic configuration and embedded servers.

Without Spring Boot: You'd manually configure database connections, transaction managers, web servers, view resolvers - hundreds of lines of XML or Java configuration.

With Spring Boot: You add dependencies and Spring Boot auto-configures everything. Add `spring-boot-starter-web`, and you get Tomcat server, DispatcherServlet, JSON converters - all configured automatically.

In PerformanceTrack, Spring Boot lets you focus on business logic (writing ReportService, ReportController) instead of infrastructure setup. It's like buying a pre-built computer instead of assembling components yourself.

---

### **Q37: What is the difference between Spring and Spring Boot?**

**Spring**: Core framework providing IoC, DI, AOP, etc. You configure everything manually.

**Spring Boot**: Built on Spring, adds auto-configuration, starter dependencies, embedded servers, and production-ready features.

Example: To create a web application in Spring, you need:
- Configure DispatcherServlet
- Configure ViewResolver
- Set up component scanning
- Configure Tomcat server
- Maybe 100+ lines of configuration

In Spring Boot:
- Add `spring-boot-starter-web` dependency
- Write `@SpringBootApplication`
- Done! All configuration happens automatically.

PerformanceTrack uses Spring Boot because it's faster to develop and less error-prone.

---

### **Q38: What is @SpringBootApplication annotation?**

`@SpringBootApplication` is a combination of three annotations:

```java
@SpringBootApplication  
// Equivalent to:
// @Configuration - Marks class as configuration
// @EnableAutoConfiguration - Enables auto-configuration
// @ComponentScan - Scans for components
public class PerformanceTrackApplication {
    public static void main(String[] args) {
        SpringApplication.run(PerformanceTrackApplication.class, args);
    }
}
```

It's the starting point of every Spring Boot application. This one annotation sets up everything - IoC container, component scanning, auto-configuration. You put it on your main class, and Spring Boot handles the rest.

---

### **Q39: What is Auto-configuration in Spring Boot?**

Auto-configuration means Spring Boot automatically configures beans based on classpath dependencies.

In PerformanceTrack:
- You add `spring-boot-starter-data-jpa` → Spring Boot auto-configures EntityManager, TransactionManager, DataSource
- You add `spring-boot-starter-web` → Spring Boot auto-configures DispatcherServlet, Tomcat, JSON converters
- You add `mysql-connector-java` → Spring Boot auto-configures MySQL DataSource

You don't write configuration code. Spring Boot detects JARs in classpath and says "I see MySQL driver, let me configure database connection using application.properties values."

It's like a smart assistant that sets up your workspace based on what tools you brought.

---

### **Q40: What are Spring Boot Starters?**

Starters are pre-packaged dependency bundles that bring related libraries together.

In PerformanceTrack `pom.xml`:
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
```

This single dependency brings:
- Spring Web MVC
- Tomcat embedded server
- Jackson for JSON
- Validation libraries
- And more...

Instead of adding 10+ individual dependencies, you add one starter. Common starters:
- `spring-boot-starter-data-jpa` - For database access
- `spring-boot-starter-web` - For REST APIs
- `spring-boot-starter-security` - For security
- `spring-boot-starter-test` - For testing

---

### **Q41: What is the purpose of application.properties file?**

`application.properties` is where you configure your Spring Boot application without touching code.

In PerformanceTrack:
```properties
# Database
spring.datasource.url=jdbc:mysql://localhost:3306/performancetrack
spring.datasource.username=root
spring.datasource.password=admin

# Server
server.port=8080

# JPA
spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true
```

When you move from development to production, just change property values (like database URL) without recompiling code. Spring Boot reads these properties at startup and configures beans accordingly.

---

### **Q42: What is the difference between application.properties and application.yml?**

Both serve the same purpose - configuration. The difference is format.

**application.properties** (traditional):
```properties
server.port=8080
spring.datasource.url=jdbc:mysql://localhost:3306/db
spring.datasource.username=root
```

**application.yml** (hierarchical):
```yml
server:
  port: 8080
spring:
  datasource:
    url: jdbc:mysql://localhost:3306/db
    username: root
```

YAML is more readable for complex configurations because of hierarchy. Properties file is simpler for small configurations. For PerformanceTrack, either works fine. Many developers prefer properties because it's straightforward.

---

### **Q43: How do you run a Spring Boot application?**

Three common ways:

**1. IDE (Eclipse/IntelliJ):**
Right-click main class → Run as Java Application

**2. Maven:**
```
mvn spring-boot:run
```

**3. JAR file:**
```
mvn clean package
java -jar target/performancetrack.jar
```

In all cases, Spring Boot:
- Starts embedded Tomcat server
- Scans for components
- Auto-configures beans
- Makes your application available at `http://localhost:8080`

For PerformanceTrack development, you'll use method 1 (IDE). For production deployment, you'll use method 3 (JAR file).

---

### **Q44: What is the embedded server in Spring Boot?**

Spring Boot includes Tomcat server inside your JAR file. You don't need to install Tomcat separately.

Traditional way: You create a WAR file, install Tomcat server separately, deploy WAR to Tomcat.

Spring Boot way: You create a JAR file that contains your code + Tomcat. Run the JAR, and Tomcat starts automatically.

In PerformanceTrack, when you run the application, Spring Boot starts Tomcat on port 8080. Your entire application runs from a single JAR file. It's portable - copy the JAR to any machine with Java, and it runs.

---

### **Q45: Can you change the default embedded server in Spring Boot?**

Yes, you can replace Tomcat with Jetty or Undertow.

To use Jetty in PerformanceTrack:
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
    <exclusions>
        <exclusion>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-tomcat</artifactId>
        </exclusion>
    </exclusions>
</dependency>

<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-jetty</artifactId>
</dependency>
```

Most applications stick with Tomcat because it's well-tested and widely used. Jetty might be lighter, Undertow might be faster, but differences are small for typical applications like PerformanceTrack.

---

**Should I continue? I have many more questions on:**
- Spring Boot Actuator
- Profiles
- DevTools
- CommandLineRunner
- Spring Boot testing
- Then moving to Spring REST and AOP...

Let me know!

## **SPRING BOOT Interview Questions (Continued)**

### **Q46: What is Spring Boot Actuator?**

Actuator provides production-ready features to monitor and manage your application.

In PerformanceTrack, add this dependency:
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

Now you get endpoints like:
- `/actuator/health` - Check if application is running
- `/actuator/info` - Application information
- `/actuator/metrics` - Memory usage, CPU, HTTP requests
- `/actuator/env` - Environment properties

In production, your operations team can hit these endpoints to monitor PerformanceTrack's health without looking at logs. It's like a dashboard for your application's vital signs.

---

### **Q47: What are Spring Profiles?**

Profiles let you have different configurations for different environments (dev, test, production).

In PerformanceTrack, create multiple property files:
- `application.properties` (common settings)
- `application-dev.properties` (development settings)
- `application-prod.properties` (production settings)

**application-dev.properties:**
```properties
spring.datasource.url=jdbc:mysql://localhost:3306/performancetrack_dev
spring.jpa.show-sql=true
```

**application-prod.properties:**
```properties
spring.datasource.url=jdbc:mysql://prod-server:3306/performancetrack_prod
spring.jpa.show-sql=false
```

Activate profile:
```properties
spring.profiles.active=dev
```

Spring Boot loads common properties + dev-specific properties. In production, change to `spring.profiles.active=prod`. Same code, different configurations.

---

### **Q48: How do you enable a specific profile in Spring Boot?**

Three ways to activate profiles:

**1. application.properties:**
```properties
spring.profiles.active=dev
```

**2. Command line:**
```
java -jar performancetrack.jar --spring.profiles.active=prod
```

**3. Environment variable:**
```
export SPRING_PROFILES_ACTIVE=prod
java -jar performancetrack.jar
```

In PerformanceTrack development, use method 1. In production deployment, use method 2 or 3 so you can change profiles without modifying property files.

---

### **Q49: What is @Profile annotation?**

`@Profile` makes beans conditional - they're created only when specific profile is active.

In PerformanceTrack:
```java
@Configuration
public class DatabaseConfig {
    
    @Bean
    @Profile("dev")
    public DataSource devDataSource() {
        // Return H2 in-memory database for development
    }
    
    @Bean
    @Profile("prod")
    public DataSource prodDataSource() {
        // Return MySQL database for production
    }
}
```

When `dev` profile is active, Spring creates `devDataSource` bean. When `prod` profile is active, Spring creates `prodDataSource` bean. This keeps development and production configurations separate.

---

### **Q50: What is Spring Boot DevTools?**

DevTools provides developer-friendly features during development.

Add dependency:
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-devtools</artifactId>
</dependency>
```

Key features:
- **Automatic restart**: When you modify code and save, application restarts automatically
- **Live reload**: Browser refreshes automatically when you change HTML/CSS
- **Disable caching**: Templates are not cached, so you see changes immediately

In PerformanceTrack development, when you modify `ReportController` and save, the application restarts in seconds instead of you manually stopping and starting. This saves time during development.

Note: DevTools is automatically disabled in production JARs, so no performance impact.

---

### **Q51: What is CommandLineRunner in Spring Boot?**

`CommandLineRunner` runs code immediately after Spring Boot application starts.

In PerformanceTrack, you might want to insert sample data when application starts:
```java
@Component
public class DataInitializer implements CommandLineRunner {
    
    @Autowired
    private UserRepository userRepository;
    
    @Override
    public void run(String... args) throws Exception {
        System.out.println("Inserting sample users...");
        User admin = new User("admin", "admin@company.com");
        userRepository.save(admin);
        System.out.println("Sample data inserted!");
    }
}
```

When PerformanceTrack starts, this code runs once. It's useful for initialization tasks like creating default admin user, loading configuration, or warming up caches.

---

### **Q52: What is the difference between CommandLineRunner and ApplicationRunner?**

Both run code at startup, but they receive arguments differently.

**CommandLineRunner**: Receives raw String arguments
```java
@Component
public class MyRunner implements CommandLineRunner {
    @Override
    public void run(String... args) throws Exception {
        // args are raw strings
    }
}
```

**ApplicationRunner**: Receives parsed ApplicationArguments
```java
@Component
public class MyRunner implements ApplicationRunner {
    @Override
    public void run(ApplicationArguments args) throws Exception {
        // args.getOptionNames(), args.getOptionValues("name")
    }
}
```

For PerformanceTrack, you'll likely use CommandLineRunner because it's simpler. ApplicationRunner is useful when you need to parse complex command-line arguments, which is rare in typical applications.

---

### **Q53: How do you handle exceptions globally in Spring Boot?**

Use `@ControllerAdvice` with `@ExceptionHandler` to handle exceptions in one place.

In PerformanceTrack:
```java
@ControllerAdvice
public class GlobalExceptionHandler {
    
    @ExceptionHandler(ResourceNotFoundException.class)
    public ResponseEntity<String> handleNotFound(ResourceNotFoundException ex) {
        return ResponseEntity.status(HttpStatus.NOT_FOUND).body(ex.getMessage());
    }
    
    @ExceptionHandler(Exception.class)
    public ResponseEntity<String> handleGeneral(Exception ex) {
        return ResponseEntity.status(HttpStatus.INTERNAL_SERVER_ERROR)
                            .body("Something went wrong");
    }
}
```

Now, when any controller throws `ResourceNotFoundException`, this handler catches it and returns proper HTTP 404 response. You don't need try-catch in every controller method. It's centralized exception handling.

---

### **Q54: What is @RestControllerAdvice?**

`@RestControllerAdvice` is a combination of `@ControllerAdvice` + `@ResponseBody`. It's specifically for REST APIs.

```java
@RestControllerAdvice  // Instead of @ControllerAdvice
public class GlobalExceptionHandler {
    
    @ExceptionHandler(ValidationException.class)
    public ErrorResponse handleValidation(ValidationException ex) {
        return new ErrorResponse("VALIDATION_ERROR", ex.getMessage());
    }
}
```

The difference: `@RestControllerAdvice` automatically converts returned objects to JSON (because of `@ResponseBody`). For PerformanceTrack REST API, always use `@RestControllerAdvice` instead of `@ControllerAdvice`.

---

### **Q55: How do you externalize configuration in Spring Boot?**

Spring Boot allows configuration from multiple sources in priority order:

1. Command line arguments: `java -jar app.jar --server.port=9090`
2. Environment variables: `SERVER_PORT=9090`
3. application.properties in current directory
4. application.properties in classpath

In PerformanceTrack production:
- Keep application.properties with default values in JAR
- Override using environment variables: `SPRING_DATASOURCE_URL=jdbc:mysql://prod-db:3306/db`
- Or use external application.properties file

This way, you don't need to rebuild JAR for configuration changes. Just change environment variables and restart application.

---

### **Q56: What is @ConfigurationProperties?**

`@ConfigurationProperties` binds multiple related properties to a Java class.

In PerformanceTrack, instead of multiple `@Value` annotations:
```java
@Component
@ConfigurationProperties(prefix = "app.report")
public class ReportConfig {
    private int maxRecords;
    private String exportFormat;
    private boolean enableCache;
    
    // getters and setters
}
```

**application.properties:**
```properties
app.report.maxRecords=1000
app.report.exportFormat=PDF
app.report.enableCache=true
```

Spring Boot automatically maps properties to class fields. You can then autowire `ReportConfig` instead of using multiple `@Value` annotations. It's cleaner and type-safe.

---

### **Q57: How do you create custom auto-configuration in Spring Boot?**

You rarely need this in normal applications, but here's the concept:

Create a configuration class:
```java
@Configuration
@ConditionalOnClass(SomeLibrary.class)
public class MyAutoConfiguration {
    
    @Bean
    @ConditionalOnMissingBean
    public MyService myService() {
        return new MyService();
    }
}
```

Create `META-INF/spring.factories`:
```
org.springframework.boot.autoconfigure.EnableAutoConfiguration=\
com.example.MyAutoConfiguration
```

Spring Boot will auto-configure `MyService` if `SomeLibrary` is in classpath and no custom `MyService` bean exists. This is how Spring Boot's own auto-configurations work. For PerformanceTrack, you won't create custom auto-configurations - you'll just use Spring Boot's built-in ones.

---

### **Q58: What is the use of @Conditional annotations?**

`@Conditional` annotations make bean creation conditional based on certain criteria.

Common ones:
- `@ConditionalOnClass`: Create bean only if certain class exists in classpath
- `@ConditionalOnMissingBean`: Create bean only if no other bean of same type exists
- `@ConditionalOnProperty`: Create bean based on property value

Example in PerformanceTrack:
```java
@Bean
@ConditionalOnProperty(name = "app.feature.email", havingValue = "true")
public EmailService emailService() {
    return new EmailService();
}
```

`EmailService` bean is created only if `app.feature.email=true` in properties. This is useful for feature flags - enable/disable features without code changes.

---

### **Q59: How does Spring Boot achieve auto-configuration?**

Spring Boot uses `@Conditional` annotations extensively in auto-configuration classes.

When PerformanceTrack starts:
1. Spring Boot scans JARs in classpath
2. Finds auto-configuration classes (like `DataSourceAutoConfiguration`)
3. Checks conditions (`@ConditionalOnClass(DataSource.class)`)
4. If conditions met, creates beans
5. Uses properties from application.properties to configure beans

For example, `DataSourceAutoConfiguration` says: "If I see MySQL driver JAR and `spring.datasource.url` property, I'll create a DataSource bean configured with that URL."

You benefit from auto-configuration without knowing these internal details. Just add dependencies and properties, Spring Boot handles the rest.

---

### **Q60: What is the difference between @SpringBootTest and @WebMvcTest?**

Both are testing annotations but test different layers.

**@SpringBootTest**: Loads complete application context (all beans). Used for integration testing.
```java
@SpringBootTest
public class ReportServiceIntegrationTest {
    @Autowired
    private ReportService reportService;
    
    @Test
    public void testCalculateAverageRating() {
        // Test with real database and all dependencies
    }
}
```

**@WebMvcTest**: Loads only web layer (controllers). Used for controller testing.
```java
@WebMvcTest(ReportController.class)
public class ReportControllerTest {
    @Autowired
    private MockMvc mockMvc;
    
    @MockBean
    private ReportService reportService;  // Mock the service
    
    @Test
    public void testGetReport() throws Exception {
        mockMvc.perform(get("/api/reports/1"))
               .andExpect(status().isOk());
    }
}
```

`@SpringBootTest` is slower (loads everything) but tests real integration. `@WebMvcTest` is faster (loads only controller layer) but needs mocked dependencies.

---

## **SPRING REST Interview Questions**

### **Q61: What is REST?**

REST (Representational State Transfer) is an architectural style for web services using HTTP protocol.

In simple terms: REST lets different applications talk to each other over the internet using standard HTTP methods (GET, POST, PUT, DELETE).

PerformanceTrack's REST API allows:
- Frontend application to get employee performance data (GET request)
- Manager to submit review (POST request)
- HR to update goal status (PUT request)
- Admin to delete old records (DELETE request)

REST uses URLs (like `/api/reports/123`) and HTTP methods to perform operations. It's stateless - each request contains all information needed, server doesn't remember previous requests.

---

### **Q62: What are the main HTTP methods used in REST?**

Four main methods (called verbs):

**GET**: Retrieve data (doesn't modify anything)
- Example: `GET /api/reports/123` - Get report with ID 123

**POST**: Create new data
- Example: `POST /api/reports` with data in body - Create new report

**PUT**: Update existing data (replace completely)
- Example: `PUT /api/reports/123` with data in body - Update report 123

**DELETE**: Remove data
- Example: `DELETE /api/reports/123` - Delete report 123

In PerformanceTrack, you use:
- GET to fetch reports, goals, reviews
- POST to create new performance goals
- PUT to update review status
- DELETE to remove old feedback

There's also **PATCH** (partial update) but it's less commonly used.

---

### **Q63: What is @RestController annotation?**

`@RestController` marks a class as a REST API controller that returns data (usually JSON) instead of HTML pages.

```java
@RestController
@RequestMapping("/api/reports")
public class ReportController {
    
    @GetMapping("/{id}")
    public Report getReport(@PathVariable Long id) {
        return reportService.getReport(id);  // Automatically converted to JSON
    }
}
```

`@RestController` = `@Controller` + `@ResponseBody`

The `@ResponseBody` part means: "Don't look for HTML views, just convert the returned object to JSON and send it in HTTP response."

In PerformanceTrack, all your controllers are `@RestController` because you're building a REST API, not a traditional web application with HTML pages.

---

### **Q64: What is the difference between @Controller and @RestController?**

**@Controller**: Returns view names (HTML pages). Used in traditional MVC applications.
```java
@Controller
public class ViewController {
    @GetMapping("/home")
    public String home() {
        return "home";  // Returns home.html page
    }
}
```

**@RestController**: Returns data objects (JSON). Used in REST APIs.
```java
@RestController
public class ApiController {
    @GetMapping("/user")
    public User getUser() {
        return new User("John", "john@example.com");  // Returns JSON
    }
}
```

For PerformanceTrack (REST API), always use `@RestController`. If you were building a traditional web app with HTML pages, you'd use `@Controller`.

---

### **Q65: What is @RequestMapping annotation?**

`@RequestMapping` maps HTTP requests to handler methods.

```java
@RestController
@RequestMapping("/api/reports")  // Base path
public class ReportController {
    
    @RequestMapping(value = "/{id}", method = RequestMethod.GET)
    public Report getReport(@PathVariable Long id) {
        return reportService.getReport(id);
    }
}
```

This maps `GET /api/reports/123` to `getReport()` method.

`@RequestMapping` is generic - you specify the HTTP method. There are shortcut annotations:
- `@GetMapping` = `@RequestMapping(method = RequestMethod.GET)`
- `@PostMapping` = `@RequestMapping(method = RequestMethod.POST)`
- `@PutMapping` = `@RequestMapping(method = RequestMethod.PUT)`
- `@DeleteMapping` = `@RequestMapping(method = RequestMethod.DELETE)`

In modern code, use shortcuts (`@GetMapping`, `@PostMapping`) instead of `@RequestMapping` - they're clearer.

---

### **Q66: What is @GetMapping, @PostMapping, @PutMapping, @DeleteMapping?**

These are shortcut annotations for common HTTP methods.

In PerformanceTrack's ReportController:
```java
@RestController
@RequestMapping("/api/reports")
public class ReportController {
    
    @GetMapping("/{id}")  // GET /api/reports/123
    public Report getReport(@PathVariable Long id) {
        return reportService.getReport(id);
    }
    
    @PostMapping  // POST /api/reports
    public Report createReport(@RequestBody Report report) {
        return reportService.saveReport(report);
    }
    
    @PutMapping("/{id}")  // PUT /api/reports/123
    public Report updateReport(@PathVariable Long id, @RequestBody Report report) {
        return reportService.updateReport(id, report);
    }
    
    @DeleteMapping("/{id}")  // DELETE /api/reports/123
    public void deleteReport(@PathVariable Long id) {
        reportService.deleteReport(id);
    }
}
```

These are clearer and more concise than using `@RequestMapping(method = ...)`.

---

### **Q67: What is @PathVariable annotation?**

`@PathVariable` extracts values from the URL path.

In PerformanceTrack:
```java
@GetMapping("/reports/{reportId}/reviews/{reviewId}")
public Review getReview(@PathVariable Long reportId, 
                       @PathVariable Long reviewId) {
    return reviewService.getReview(reportId, reviewId);
}
```

When frontend calls `GET /api/reports/5/reviews/10`:
- `reportId` gets value 5
- `reviewId` gets value 10

The variable name must match the placeholder in curly braces, or you can specify:
```java
@PathVariable("reportId") Long id
```

Path variables are for identifying resources. In PerformanceTrack, you use them for IDs (employee ID, goal ID, review ID).

---

### **Q68: What is @RequestParam annotation?**

`@RequestParam` extracts query parameters from URL.

In PerformanceTrack:
```java
@GetMapping("/reports")
public List<Report> getReports(@RequestParam String department,
                               @RequestParam(required = false) Integer year) {
    return reportService.getReportsByDepartment(department, year);
}
```

When frontend calls `GET /api/reports?department=Engineering&year=2024`:
- `department` gets value "Engineering"
- `year` gets value 2024

Query parameters are for filtering, sorting, pagination. Use them when you need optional parameters.

`required = false` means the parameter is optional. Default is `required = true`.

---

### **Q69: What is the difference between @PathVariable and @RequestParam?**

**@PathVariable**: Part of URL path (mandatory, identifies resource)
```java
GET /api/employees/123  // 123 is path variable
@GetMapping("/employees/{id}")
public Employee getEmployee(@PathVariable Long id)
```

**@RequestParam**: Query parameters (optional, filters/modifies response)
```java
GET /api/employees?department=IT&status=active  // department and status are request params
@GetMapping("/employees")
public List<Employee> getEmployees(@RequestParam String department,
                                   @RequestParam String status)
```

Rule of thumb:
- Use `@PathVariable` for resource identifiers (employee ID, report ID)
- Use `@RequestParam` for filters, search, pagination, sorting

In PerformanceTrack:
- `/api/reports/123` - path variable for report ID
- `/api/reports?year=2024&department=IT` - request params for filtering

---

### **Q70: What is @RequestBody annotation?**

`@RequestBody` binds HTTP request body (usually JSON) to a Java object.

In PerformanceTrack:
```java
@PostMapping("/goals")
public Goal createGoal(@RequestBody Goal goal) {
    return goalService.saveGoal(goal);
}
```

When frontend sends POST request with JSON body:
```json
{
  "title": "Improve code quality",
  "description": "Reduce bugs by 20%",
  "deadline": "2024-12-31"
}
```

Spring automatically converts this JSON to `Goal` object. You don't manually parse JSON - Spring's Jackson library does it.

Always use `@RequestBody` for POST and PUT requests where you're sending data to server.

---

### **Q71: What is @ResponseBody annotation?**

`@ResponseBody` tells Spring to convert the returned object to JSON and write it to HTTP response.

```java
@Controller  // Not RestController
public class ReportController {
    
    @GetMapping("/report")
    @ResponseBody  // Without this, Spring looks for report.html view
    public Report getReport() {
        return new Report();  // Converted to JSON
    }
}
```

When you use `@RestController`, you don't need `@ResponseBody` because `@RestController` includes it automatically.

In PerformanceTrack, you use `@RestController`, so you never need to write `@ResponseBody` explicitly.

---

### **Q72: What is ResponseEntity?**

`ResponseEntity` gives you full control over HTTP response - status code, headers, and body.

In PerformanceTrack:
```java
@GetMapping("/reports/{id}")
public ResponseEntity<Report> getReport(@PathVariable Long id) {
    Report report = reportService.getReport(id);
    
    if (report == null) {
        return ResponseEntity.notFound().build();  // 404 Not Found
    }
    
    return ResponseEntity.ok(report);  // 200 OK with report in body
}
```

You can also set custom headers:
```java
return ResponseEntity
    .status(HttpStatus.CREATED)  // 201 Created
    .header("Custom-Header", "value")
    .body(newReport);
```

Use `ResponseEntity` when you need to control HTTP status codes. For simple cases, just return the object directly.

---

### **Q73: What are HTTP status codes and which ones are commonly used in REST APIs?**

Status codes tell the client what happened with their request.

**2xx - Success:**
- **200 OK**: Request succeeded (GET, PUT)
- **201 Created**: New resource created (POST)
- **204 No Content**: Succeeded but no data to return (DELETE)

**4xx - Client Error:**
- **400 Bad Request**: Invalid data sent
- **401 Unauthorized**: Not authenticated
- **403 Forbidden**: Authenticated but not authorized
- **404 Not Found**: Resource doesn't exist

**5xx - Server Error:**
- **500 Internal Server Error**: Something went wrong on server

In PerformanceTrack:
- GET report found → 200
- POST create goal → 201
- GET report not found → 404
- POST with invalid data → 400
- DELETE goal → 204

---

### **Q74: How do you handle validations in Spring REST?**

Use Bean Validation annotations (`@NotNull`, `@NotBlank`, `@Size`, etc.) with `@Valid`.

In PerformanceTrack:
```java
public class Goal {
    @NotBlank(message = "Title is required")
    private String title;
    
    @Size(max = 500, message = "Description too long")
    private String description;
    
    @NotNull(message = "Deadline is required")
    private LocalDate deadline;
}
```

In controller:
```java
@PostMapping("/goals")
public ResponseEntity<Goal> createGoal(@Valid @RequestBody Goal goal) {
    return ResponseEntity.status(HttpStatus.CREATED)
                        .body(goalService.saveGoal(goal));
}
```

If validation fails (e.g., title is blank), Spring automatically returns 400 Bad Request with error details. You don't write if-else checks manually.

---

### **Q75: What is Content Negotiation in REST?**

Content negotiation lets client specify what format they want (JSON, XML, etc.).

Client sends `Accept` header:
```
Accept: application/json  // I want JSON
```

Server responds with `Content-Type` header:
```
Content-Type: application/json  // I'm sending JSON
```

In PerformanceTrack, you'll mostly use JSON. Spring Boot configures JSON by default. If you need XML support, add dependency and Spring handles both:
```java
@GetMapping(value = "/reports/{id}", 
            produces = {MediaType.APPLICATION_JSON_VALUE, 
                       MediaType.APPLICATION_XML_VALUE})
public Report getReport(@PathVariable Long id) {
    return reportService.getReport(id);
}
```

Based on `Accept` header, Spring returns JSON or XML. For typical projects, JSON is enough.

---

**Should I continue? I have many more questions on:**
- REST best practices
- HATEOAS
- Pagination
- CORS
- File uploads/downloads
- Then moving to AOP (Aspect-Oriented Programming)...

Let me know!

## **SPRING REST Interview Questions (Continued)**

### **Q76: What is CORS and how do you handle it in Spring Boot?**

CORS (Cross-Origin Resource Sharing) is a security feature that blocks requests from different domains.

Imagine PerformanceTrack backend runs on `http://localhost:8080` and frontend runs on `http://localhost:3000`. When frontend tries to call your API, browser blocks it by default for security.

Solution - Enable CORS:

**Method 1: On specific controller**
```java
@RestController
@CrossOrigin(origins = "http://localhost:3000")
public class ReportController {
    // Now frontend at localhost:3000 can call this controller
}
```

**Method 2: Global configuration**
```java
@Configuration
public class WebConfig implements WebMvcConfigurer {
    @Override
    public void addCorsMappings(CorsRegistry registry) {
        registry.addMapping("/api/**")
                .allowedOrigins("http://localhost:3000")
                .allowedMethods("GET", "POST", "PUT", "DELETE");
    }
}
```

In production, replace `localhost:3000` with your actual frontend domain.

---

### **Q77: How do you implement pagination in Spring REST API?**

Use Spring Data's `Pageable` interface for automatic pagination.

In PerformanceTrack:
```java
@GetMapping("/reports")
public Page<Report> getReports(Pageable pageable) {
    return reportRepository.findAll(pageable);
}
```

Frontend calls with pagination parameters:
```
GET /api/reports?page=0&size=10&sort=createdDate,desc
```

This returns:
- Page 0 (first page)
- 10 records per page
- Sorted by createdDate descending

Response includes:
```json
{
  "content": [...],  // Actual data
  "totalElements": 100,
  "totalPages": 10,
  "number": 0,  // Current page
  "size": 10
}
```

Spring handles everything automatically - you just use `Pageable` parameter.

---

### **Q78: What is the difference between Page and Slice in Spring Data?**

Both are used for pagination, but with different features:

**Page**: Contains total count of records
```java
public Page<Report> getReports(Pageable pageable) {
    return reportRepository.findAll(pageable);  // Executes COUNT query
}
```
Response includes `totalElements` and `totalPages`. Good for showing "Page 1 of 10" in UI.

**Slice**: Doesn't contain total count
```java
public Slice<Report> getReports(Pageable pageable) {
    return reportRepository.findAll(pageable);  // No COUNT query
}
```
Only knows if there's a next page. Good for "Load More" button in UI.

Use **Page** when you need page numbers (1, 2, 3...). Use **Slice** for infinite scrolling (slightly faster because no COUNT query).

For PerformanceTrack, **Page** is better for admin reports where you show total results.

---

### **Q79: How do you implement sorting in Spring REST API?**

Sorting is built into `Pageable`.

In PerformanceTrack:
```java
@GetMapping("/goals")
public List<Goal> getGoals(Pageable pageable) {
    return goalRepository.findAll(pageable).getContent();
}
```

Frontend calls:
```
GET /api/goals?sort=deadline,asc
GET /api/goals?sort=title,desc
GET /api/goals?sort=priority,desc&sort=deadline,asc  // Multiple sorts
```

You can also use `Sort` directly without pagination:
```java
@GetMapping("/goals")
public List<Goal> getGoals() {
    Sort sort = Sort.by(Sort.Direction.DESC, "priority");
    return goalRepository.findAll(sort);
}
```

Spring translates this to `ORDER BY` clause in SQL automatically.

---

### **Q80: What is HATEOAS in REST?**

HATEOAS (Hypermedia As The Engine Of Application State) means including links in API responses so clients can navigate without hardcoding URLs.

Normal response:
```json
{
  "id": 1,
  "title": "Q1 Report"
}
```

HATEOAS response:
```json
{
  "id": 1,
  "title": "Q1 Report",
  "_links": {
    "self": {"href": "/api/reports/1"},
    "reviews": {"href": "/api/reports/1/reviews"},
    "employee": {"href": "/api/employees/5"}
  }
}
```

In PerformanceTrack, add dependency:
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-hateoas</artifactId>
</dependency>
```

Implementation:
```java
@GetMapping("/reports/{id}")
public EntityModel<Report> getReport(@PathVariable Long id) {
    Report report = reportService.getReport(id);
    EntityModel<Report> model = EntityModel.of(report);
    model.add(linkTo(methodOn(ReportController.class).getReport(id)).withSelfRel());
    return model;
}
```

HATEOAS is a REST best practice but rarely used in real projects because it adds complexity. For PerformanceTrack, simple JSON responses are fine.

---

### **Q81: How do you upload files in Spring REST API?**

Use `MultipartFile` for file uploads.

In PerformanceTrack (uploading evidence documents):
```java
@PostMapping("/evidence/upload")
public ResponseEntity<String> uploadEvidence(@RequestParam("file") MultipartFile file) {
    
    if (file.isEmpty()) {
        return ResponseEntity.badRequest().body("File is empty");
    }
    
    String fileName = file.getOriginalFilename();
    String filePath = "/uploads/" + fileName;
    
    try {
        file.transferTo(new File(filePath));
        return ResponseEntity.ok("File uploaded: " + fileName);
    } catch (IOException e) {
        return ResponseEntity.status(HttpStatus.INTERNAL_SERVER_ERROR)
                            .body("Upload failed");
    }
}
```

Frontend sends multipart form-data request. Spring automatically handles the file upload.

Configure max file size in `application.properties`:
```properties
spring.servlet.multipart.max-file-size=10MB
spring.servlet.multipart.max-request-size=10MB
```

---

### **Q82: How do you download files in Spring REST API?**

Use `InputStreamResource` or byte array with appropriate headers.

In PerformanceTrack (downloading report as PDF):
```java
@GetMapping("/reports/{id}/download")
public ResponseEntity<Resource> downloadReport(@PathVariable Long id) throws IOException {
    
    File file = new File("/reports/report_" + id + ".pdf");
    InputStreamResource resource = new InputStreamResource(new FileInputStream(file));
    
    return ResponseEntity.ok()
            .header(HttpHeaders.CONTENT_DISPOSITION, "attachment; filename=report.pdf")
            .contentType(MediaType.APPLICATION_PDF)
            .contentLength(file.length())
            .body(resource);
}
```

The `Content-Disposition: attachment` header tells browser to download the file instead of displaying it.

For Excel files, use `MediaType.parseMediaType("application/vnd.ms-excel")`.

---

### **Q83: What is the difference between @RequestParam and @RequestBody?**

**@RequestParam**: Extracts data from URL query parameters or form data (key-value pairs)
```java
GET /api/reports?year=2024
@GetMapping("/reports")
public List<Report> getReports(@RequestParam Integer year)
```

**@RequestBody**: Extracts data from request body (usually JSON)
```java
POST /api/reports
Body: {"title": "Q1 Report", "year": 2024}

@PostMapping("/reports")
public Report createReport(@RequestBody Report report)
```

Simple rule:
- GET requests → use `@RequestParam`
- POST/PUT requests → use `@RequestBody`

In PerformanceTrack, you use `@RequestBody` when creating or updating resources (goals, reviews) and `@RequestParam` when filtering or searching.

---

### **Q84: How do you version REST APIs?**

Three common approaches:

**1. URI Versioning (most common):**
```java
@GetMapping("/api/v1/reports")  // Version 1
public List<Report> getReportsV1()

@GetMapping("/api/v2/reports")  // Version 2
public List<ReportDTO> getReportsV2()
```

**2. Request Parameter:**
```java
GET /api/reports?version=1
@GetMapping("/reports")
public List<Report> getReports(@RequestParam String version)
```

**3. Header Versioning:**
```java
GET /api/reports
Header: API-Version: 1

@GetMapping(value = "/reports", headers = "API-Version=1")
public List<Report> getReportsV1()
```

For PerformanceTrack, use **URI versioning** (`/api/v1/`) - it's simplest and most visible. When you need breaking changes, create `/api/v2/` endpoints.

---

### **Q85: What is idempotency in REST?**

Idempotent means calling the same request multiple times produces the same result.

**Idempotent methods:**
- **GET**: Calling multiple times returns same data
- **PUT**: Updating same resource multiple times = same final state
- **DELETE**: Deleting already deleted resource = still deleted

**Non-idempotent method:**
- **POST**: Creating resource multiple times = multiple resources created

Example in PerformanceTrack:
```java
PUT /api/goals/1 with {title: "Updated Goal"}  // Idempotent
Calling 10 times = goal 1 still has title "Updated Goal"

POST /api/goals with {title: "New Goal"}  // Not idempotent
Calling 10 times = 10 goals created
```

This matters for reliability. If network fails and client retries PUT, it's safe. If client retries POST, you might create duplicates (unless you add deduplication logic).

---

### **Q86: What are REST API best practices?**

Key best practices for PerformanceTrack:

**1. Use nouns for endpoints, not verbs:**
- Good: `GET /api/reports`, `POST /api/goals`
- Bad: `GET /api/getReports`, `POST /api/createGoal`

**2. Use proper HTTP methods:**
- GET for reading
- POST for creating
- PUT for updating
- DELETE for deleting

**3. Use plural nouns:**
- Good: `/api/reports`, `/api/employees`
- Bad: `/api/report`, `/api/employee`

**4. Use proper status codes:**
- 200 for success
- 201 for created
- 404 for not found
- 400 for validation errors

**5. Version your API:**
- `/api/v1/reports`

**6. Use filtering, sorting, pagination:**
- `/api/reports?department=IT&page=0&size=10`

**7. Return consistent error format:**
```json
{
  "error": "VALIDATION_ERROR",
  "message": "Title is required",
  "timestamp": "2024-12-10T10:30:00"
}
```

---

### **Q87: How do you secure REST APIs?**

Common security approaches:

**1. JWT (JSON Web Token) - Most common for REST APIs:**
```java
// User logs in, gets JWT token
POST /api/auth/login
Response: {"token": "eyJhbGciOiJIUzI1NiIs..."}

// Client sends token in header
GET /api/reports
Header: Authorization: Bearer eyJhbGciOiJIUzI1NiIs...
```

**2. Basic Authentication (simple but less secure):**
```java
Header: Authorization: Basic base64(username:password)
```

**3. OAuth2 (for third-party integrations):**
Complex but standard for enterprise applications.

In PerformanceTrack, you're using JWT authentication. User logs in with username/password, gets a token, and includes that token in all subsequent API calls. Spring Security validates the token on each request.

---

### **Q88: What is the difference between authentication and authorization?**

**Authentication**: Verifying WHO you are (identity)
- Login with username/password
- "Prove you are John"

**Authorization**: Verifying WHAT you can do (permissions)
- Checking if you have access to specific resource
- "John can view reports but cannot delete them"

In PerformanceTrack:
```java
// Authentication
POST /api/auth/login
Body: {"username": "john", "password": "pass123"}
Response: {"token": "..."}  // You are John

// Authorization
GET /api/admin/users
Header: Authorization: Bearer token
// Check if John has ADMIN role
```

Flow: First authenticate (who are you?), then authorize (what can you access?). Spring Security handles both.

---

### **Q89: How do you implement rate limiting in REST APIs?**

Rate limiting prevents abuse by limiting number of requests per user/IP.

**Using Bucket4j library:**
```java
@RestController
public class ReportController {
    
    private final Bucket bucket = Bucket.builder()
        .addLimit(Limit.of(100, Duration.ofMinutes(1)))  // 100 requests per minute
        .build();
    
    @GetMapping("/reports")
    public ResponseEntity<?> getReports() {
        if (bucket.tryConsume(1)) {
            return ResponseEntity.ok(reportService.getAllReports());
        }
        return ResponseEntity.status(HttpStatus.TOO_MANY_REQUESTS)
                            .body("Rate limit exceeded");
    }
}
```

In production, rate limiting is often done at API Gateway level (like AWS API Gateway, Kong) rather than in application code.

For PerformanceTrack college project, rate limiting is optional - it's more of an enterprise feature.

---

### **Q90: What is Richardson Maturity Model for REST?**

It's a model that measures how RESTful your API is (0 to 3):

**Level 0 - The Swamp of POX:**
Single URI, single HTTP method (usually POST)
```
POST /api/service
Body: {"action": "getReport", "id": 1}
```
Not RESTful at all.

**Level 1 - Resources:**
Multiple URIs, but still single method
```
POST /api/reports/1
POST /api/goals/5
```
Better, but still not using HTTP methods properly.

**Level 2 - HTTP Verbs:**
Multiple URIs + proper HTTP methods
```
GET /api/reports/1
POST /api/goals
PUT /api/reviews/5
DELETE /api/feedback/10
```
This is what most REST APIs use, including PerformanceTrack.

**Level 3 - Hypermedia (HATEOAS):**
Level 2 + links in responses
```json
{
  "id": 1,
  "title": "Report",
  "_links": {
    "self": "/api/reports/1",
    "reviews": "/api/reports/1/reviews"
  }
}
```
Most RESTful, but complex and rarely used.

PerformanceTrack is Level 2, which is perfectly fine for most applications.

---

## **AOP (Aspect-Oriented Programming) Interview Questions**

### **Q91: What is AOP (Aspect-Oriented Programming)?**

AOP is a programming technique that lets you add behavior to existing code without modifying that code. It helps separate cross-cutting concerns from business logic.

**Cross-cutting concerns** are functionalities needed across multiple places:
- Logging
- Security checks
- Transaction management
- Performance monitoring
- Exception handling

Without AOP in PerformanceTrack:
```java
public Report getReport(Long id) {
    logger.info("Getting report: " + id);  // Logging
    long startTime = System.currentTimeMillis();  // Performance
    // Check security...
    Report report = reportRepository.findById(id);
    long endTime = System.currentTimeMillis();
    logger.info("Time taken: " + (endTime - startTime));
    return report;
}
```
Logging and timing code clutters business logic.

With AOP:
```java
public Report getReport(Long id) {
    return reportRepository.findById(id);  // Clean business logic
}
```
AOP handles logging and timing separately.

---

### **Q92: What are the core concepts of AOP?**

**1. Aspect**: A module that contains cross-cutting logic (like logging)
**2. Join Point**: A point in program execution (method call, exception thrown)
**3. Advice**: Action taken at a join point (before, after, around)
**4. Pointcut**: Expression that selects which join points to apply advice
**5. Weaving**: Process of applying aspects to target objects

Think of it like:
- **Aspect** = A security guard
- **Join Point** = Every door in building
- **Pointcut** = Only main entrance and server room doors
- **Advice** = Check ID before entering
- **Weaving** = Installing the security system

In PerformanceTrack, you create a logging aspect that automatically logs all controller methods.

---

### **Q93: What is an Aspect in AOP?**

An Aspect is a class that contains cross-cutting logic, marked with `@Aspect`.

In PerformanceTrack:
```java
@Aspect
@Component
public class LoggingAspect {
    
    @Before("execution(* com.performancetrack.controller.*.*(..))")
    public void logBeforeControllerMethods(JoinPoint joinPoint) {
        String methodName = joinPoint.getSignature().getName();
        System.out.println("Calling method: " + methodName);
    }
}
```

This aspect automatically logs whenever any controller method is called. You don't add logging code to each controller method - the aspect handles it.

`@Aspect` tells Spring "This is an aspect". `@Component` makes it a Spring bean.

---

### **Q94: What is a Join Point in AOP?**

Join Point is a point in your program where aspect can be applied - like method execution, object creation, exception thrown.

In practice, it's usually a **method execution**.

In PerformanceTrack, when you call:
```java
reportController.getReport(1);
```

This method execution is a join point. Your logging aspect can intercept at this point.

The `JoinPoint` parameter in advice gives you information:
```java
@Before("execution(* com.performancetrack.service.*.*(..))")
public void logService(JoinPoint joinPoint) {
    String className = joinPoint.getTarget().getClass().getName();
    String methodName = joinPoint.getSignature().getName();
    Object[] args = joinPoint.getArgs();
    System.out.println(className + "." + methodName + " called with " + Arrays.toString(args));
}
```

---

### **Q95: What is Advice in AOP?**

Advice is the action taken by an aspect at a join point. There are five types:

**1. @Before**: Runs before method execution
```java
@Before("execution(* com.performancetrack.service.*.*(..))")
public void beforeAdvice(JoinPoint joinPoint) {
    System.out.println("Before calling: " + joinPoint.getSignature().getName());
}
```

**2. @After**: Runs after method execution (whether success or failure)
```java
@After("execution(* com.performancetrack.service.*.*(..))")
public void afterAdvice(JoinPoint joinPoint) {
    System.out.println("After calling: " + joinPoint.getSignature().getName());
}
```

**3. @AfterReturning**: Runs after successful method execution
```java
@AfterReturning(pointcut = "execution(* com.performancetrack.service.*.*(..))", 
                returning = "result")
public void afterReturningAdvice(JoinPoint joinPoint, Object result) {
    System.out.println("Method returned: " + result);
}
```

**4. @AfterThrowing**: Runs if method throws exception
```java
@AfterThrowing(pointcut = "execution(* com.performancetrack.service.*.*(..))", 
               throwing = "error")
public void afterThrowingAdvice(JoinPoint joinPoint, Throwable error) {
    System.out.println("Method threw: " + error.getMessage());
}
```

**5. @Around**: Most powerful - wraps around method execution
```java
@Around("execution(* com.performancetrack.service.*.*(..))")
public Object aroundAdvice(ProceedingJoinPoint joinPoint) throws Throwable {
    System.out.println("Before method");
    Object result = joinPoint.proceed();  // Actual method execution
    System.out.println("After method");
    return result;
}
```

---

### **Q96: What is a Pointcut in AOP?**

Pointcut is an expression that selects which join points (methods) to apply advice on.

Common pointcut expressions:

**1. All methods in a package:**
```java
execution(* com.performancetrack.controller.*.*(..))
```
- First `*` = any return type
- `com.performancetrack.controller.*` = any class in controller package
- Second `*` = any method name
- `(..)` = any parameters

**2. Specific method:**
```java
execution(* com.performancetrack.service.ReportService.getReport(..))
```

**3. Methods with specific return type:**
```java
execution(Report com.performancetrack.service.*.*(..))
```

**4. Methods with annotation:**
```java
@annotation(org.springframework.web.bind.annotation.GetMapping)
```

In PerformanceTrack, you might use:
```java
@Before("execution(* com.performancetrack.controller.*.*(..))")
```
This applies advice to all controller methods.

---

### **Q97: What is the difference between @Before and @Around advice?**

**@Before**: Runs before method, cannot prevent method execution
```java
@Before("execution(* com.performancetrack.service.*.*(..))")
public void beforeAdvice(JoinPoint joinPoint) {
    System.out.println("Before method");
    // Method will execute no matter what
}
```

**@Around**: Wraps method, can prevent execution and modify result
```java
@Around("execution(* com.performancetrack.service.*.*(..))")
public Object aroundAdvice(ProceedingJoinPoint joinPoint) throws Throwable {
    System.out.println("Before method");
    
    // You control if method executes
    if (someCondition) {
        return null;  // Method doesn't execute
    }
    
    Object result = joinPoint.proceed();  // Execute method
    
    System.out.println("After method");
    return result;  // Can modify result
}
```

Use `@Before` for simple logging. Use `@Around` when you need full control - performance monitoring, caching, security checks.

---

### **Q98: How do you implement logging using AOP?**

Create a logging aspect in PerformanceTrack:

```java
@Aspect
@Component
public class LoggingAspect {
    
    private static final Logger logger = LoggerFactory.getLogger(LoggingAspect.class);
    
    @Before("execution(* com.performancetrack.controller.*.*(..))")
    public void logControllerMethods(JoinPoint joinPoint) {
        String className = joinPoint.getTarget().getClass().getSimpleName();
        String methodName = joinPoint.getSignature().getName();
        Object[] args = joinPoint.getArgs();
        
        logger.info("Controller: {}.{} called with args: {}", 
                   className, methodName, Arrays.toString(args));
    }
    
    @AfterReturning(pointcut = "execution(* com.performancetrack.service.*.*(..))", 
                    returning = "result")
    public void logServiceMethods(JoinPoint joinPoint, Object result) {
        String methodName = joinPoint.getSignature().getName();
        logger.info("Service: {} returned: {}", methodName, result);
    }
}
```

Add AOP dependency:
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-aop</artifactId>
</dependency>
```

Now all controller and service methods are logged automatically without modifying their code.

---

### **Q99: How do you implement performance monitoring using AOP?**

Use `@Around` advice to measure execution time:

```java
@Aspect
@Component
public class PerformanceAspect {
    
    private static final Logger logger = LoggerFactory.getLogger(PerformanceAspect.class);
    
    @Around("execution(* com.performancetrack.service.*.*(..))")
    public Object monitorPerformance(ProceedingJoinPoint joinPoint) throws Throwable {
        long startTime = System.currentTimeMillis();
        
        String methodName = joinPoint.getSignature().getName();
        
        Object result = joinPoint.proceed();  // Execute method
        
        long endTime = System.currentTimeMillis();
        long duration = endTime - startTime;
        
        logger.info("Method {} took {} ms", methodName, duration);
        
        if (duration > 1000) {
            logger.warn("SLOW METHOD: {} took {} ms", methodName, duration);
        }
        
        return result;
    }
}
```

This automatically monitors all service methods. If any method takes over 1 second, you get a warning. You don't add timing code to each service method.

---

### **Q100: What is @Pointcut annotation used for?**

`@Pointcut` defines reusable pointcut expressions.

Instead of repeating expressions:
```java
@Before("execution(* com.performancetrack.controller.*.*(..))")
public void beforeAdvice1() { }

@After("execution(* com.performancetrack.controller.*.*(..))")
public void afterAdvice1() { }
```

Define once, reuse:
```java
@Aspect
@Component
public class LoggingAspect {
    
    @Pointcut("execution(* com.performancetrack.controller.*.*(..))")
    public void controllerMethods() {}  // Pointcut signature
    
    @Pointcut("execution(* com.performancetrack.service.*.*(..))")
    public void serviceMethods() {}
    
    @Before("controllerMethods()")
    public void beforeController() {
        System.out.println("Before controller");
    }
    
    @Before("serviceMethods()")
    public void beforeService() {
        System.out.println("Before service");
    }
    
    @Before("controllerMethods() || serviceMethods()")
    public void beforeBoth() {
        System.out.println("Before controller or service");
    }
}
```

This makes code cleaner and changes easier - update expression in one place.

---

**Should I continue with more AOP questions and then some advanced topics? Let me know!**

## **AOP (Aspect-Oriented Programming) Interview Questions (Continued)**

### **Q101: What is the order of execution for multiple aspects?**

When multiple aspects apply to same method, Spring executes them in order based on `@Order` annotation.

In PerformanceTrack:
```java
@Aspect
@Component
@Order(1)  // Executes first
public class SecurityAspect {
    @Before("execution(* com.performancetrack.controller.*.*(..))")
    public void checkSecurity() {
        System.out.println("1. Security check");
    }
}

@Aspect
@Component
@Order(2)  // Executes second
public class LoggingAspect {
    @Before("execution(* com.performancetrack.controller.*.*(..))")
    public void logMethod() {
        System.out.println("2. Logging");
    }
}

@Aspect
@Component
@Order(3)  // Executes third
public class PerformanceAspect {
    @Before("execution(* com.performancetrack.controller.*.*(..))")
    public void measurePerformance() {
        System.out.println("3. Performance monitoring");
    }
}
```

When controller method is called, output:
```
1. Security check
2. Logging
3. Performance monitoring
[Controller method executes]
```

Lower order number = higher priority. Without `@Order`, execution order is unpredictable.

---

### **Q102: What is the difference between compile-time weaving and runtime weaving?**

Weaving is the process of applying aspects to your code.

**Compile-time weaving (AspectJ):**
Aspects are applied when Java code is compiled. The bytecode is modified to include aspect logic.
- Faster runtime performance
- Requires special compiler
- More complex setup

**Runtime weaving (Spring AOP):**
Aspects are applied at runtime using proxies. Original code remains unchanged.
- Slower runtime performance (minimal difference)
- No special compiler needed
- Easy setup

Spring Boot uses **runtime weaving** by default. When you create a bean, Spring creates a proxy object that wraps your bean and applies aspects.

In PerformanceTrack:
```java
@Service
public class ReportService {
    public Report getReport(Long id) { }
}
```

At runtime, Spring creates a proxy:
```
[Client] → [Proxy (applies aspects)] → [Actual ReportService]
```

You don't notice this - Spring handles it transparently.

---

### **Q103: What is JoinPoint and ProceedingJoinPoint?**

**JoinPoint**: Provides information about the method being intercepted. Used in all advice types except `@Around`.

```java
@Before("execution(* com.performancetrack.service.*.*(..))")
public void beforeAdvice(JoinPoint joinPoint) {
    String methodName = joinPoint.getSignature().getName();
    String className = joinPoint.getTarget().getClass().getName();
    Object[] args = joinPoint.getArgs();
    
    System.out.println("Method: " + className + "." + methodName);
    System.out.println("Arguments: " + Arrays.toString(args));
}
```

**ProceedingJoinPoint**: Extends JoinPoint, used ONLY in `@Around` advice. Has `proceed()` method to execute the actual method.

```java
@Around("execution(* com.performancetrack.service.*.*(..))")
public Object aroundAdvice(ProceedingJoinPoint joinPoint) throws Throwable {
    System.out.println("Before");
    Object result = joinPoint.proceed();  // Execute method
    System.out.println("After");
    return result;
}
```

Key difference: `ProceedingJoinPoint` lets you control method execution, `JoinPoint` doesn't.

---

### **Q104: Can you modify method arguments in AOP?**

Yes, using `@Around` advice with `ProceedingJoinPoint`.

In PerformanceTrack, sanitize input automatically:
```java
@Aspect
@Component
public class InputSanitizationAspect {
    
    @Around("execution(* com.performancetrack.controller.*.*(..))")
    public Object sanitizeInput(ProceedingJoinPoint joinPoint) throws Throwable {
        Object[] args = joinPoint.getArgs();
        
        // Modify arguments
        for (int i = 0; i < args.length; i++) {
            if (args[i] instanceof String) {
                args[i] = ((String) args[i]).trim();  // Remove whitespace
            }
        }
        
        // Execute with modified arguments
        return joinPoint.proceed(args);
    }
}
```

Now all String parameters are automatically trimmed before reaching controller methods.

---

### **Q105: How do you implement exception handling using AOP?**

Use `@AfterThrowing` advice to handle exceptions globally.

In PerformanceTrack:
```java
@Aspect
@Component
public class ExceptionHandlingAspect {
    
    private static final Logger logger = LoggerFactory.getLogger(ExceptionHandlingAspect.class);
    
    @AfterThrowing(
        pointcut = "execution(* com.performancetrack.service.*.*(..))",
        throwing = "exception"
    )
    public void handleServiceExceptions(JoinPoint joinPoint, Exception exception) {
        String methodName = joinPoint.getSignature().getName();
        
        logger.error("Exception in service method: {}", methodName);
        logger.error("Exception message: {}", exception.getMessage());
        logger.error("Stack trace: ", exception);
        
        // Send alert email to admin
        // emailService.sendAlert("Error in " + methodName, exception.getMessage());
    }
}
```

This automatically logs all service layer exceptions and can send alerts. You don't add try-catch in every service method.

---

### **Q106: What are the limitations of Spring AOP?**

Spring AOP has some limitations:

**1. Only works on Spring beans:**
```java
@Service  // Must be a Spring bean
public class ReportService {
    public void getReport() { }  // AOP works
}

// Regular class - not a bean
public class Helper {
    public void doSomething() { }  // AOP doesn't work
}
```

**2. Only method-level join points:**
Can intercept method execution, but not field access or constructor calls.

**3. Cannot intercept calls within same class:**
```java
@Service
public class ReportService {
    
    @Transactional
    public void methodA() {
        methodB();  // @Transactional on methodB won't work here
    }
    
    @Transactional
    public void methodB() { }
}
```
When `methodA` calls `methodB` directly, aspect doesn't apply because it's not going through the proxy.

**4. Only public methods:**
Spring AOP works only on public methods, not private or protected.

For PerformanceTrack, these limitations rarely matter. Most aspects work on public service/controller methods.

---

### **Q107: What is the difference between Spring AOP and AspectJ?**

**Spring AOP:**
- Simpler, easier to use
- Runtime weaving using proxies
- Only method execution join points
- Works only on Spring beans
- Perfect for enterprise applications like PerformanceTrack

**AspectJ:**
- More powerful, complex
- Compile-time weaving (can also do runtime)
- All join points (field access, constructor, etc.)
- Works on any Java class
- Used when you need advanced AOP features

For PerformanceTrack and most Spring Boot projects, **Spring AOP is sufficient**. You only need AspectJ for very specific requirements like intercepting field access or constructor calls.

Spring AOP uses AspectJ's annotation style (`@Aspect`, `@Before`, etc.) but implements it differently.

---

### **Q108: How do you implement transaction management using AOP?**

Spring's `@Transactional` annotation is implemented using AOP behind the scenes.

In PerformanceTrack:
```java
@Service
public class ReportService {
    
    @Autowired
    private ReportRepository reportRepository;
    
    @Autowired
    private AuditRepository auditRepository;
    
    @Transactional  // AOP applies transaction
    public void createReport(Report report) {
        reportRepository.save(report);
        
        Audit audit = new Audit("Report created", report.getId());
        auditRepository.save(audit);
        
        // If any exception occurs, BOTH saves are rolled back
    }
}
```

What happens behind the scenes:
```java
// Spring AOP creates something like this:
public Object transactionAdvice(ProceedingJoinPoint joinPoint) {
    TransactionStatus status = transactionManager.getTransaction();
    try {
        Object result = joinPoint.proceed();
        transactionManager.commit(status);
        return result;
    } catch (Exception e) {
        transactionManager.rollback(status);
        throw e;
    }
}
```

You just use `@Transactional`, AOP handles begin transaction, commit, rollback automatically.

---

### **Q109: Can you create custom annotations and use them with AOP?**

Yes! This is a powerful pattern in PerformanceTrack.

**Step 1: Create custom annotation**
```java
@Target(ElementType.METHOD)
@Retention(RetentionPolicy.RUNTIME)
public @interface LogExecutionTime {
}
```

**Step 2: Create aspect**
```java
@Aspect
@Component
public class ExecutionTimeAspect {
    
    @Around("@annotation(LogExecutionTime)")
    public Object logExecutionTime(ProceedingJoinPoint joinPoint) throws Throwable {
        long start = System.currentTimeMillis();
        
        Object result = joinPoint.proceed();
        
        long executionTime = System.currentTimeMillis() - start;
        System.out.println(joinPoint.getSignature() + " executed in " + executionTime + "ms");
        
        return result;
    }
}
```

**Step 3: Use annotation**
```java
@Service
public class ReportService {
    
    @LogExecutionTime  // Automatically logs execution time
    public Report generateReport(Long id) {
        // Complex calculation
        return report;
    }
}
```

This is cleaner than pointcut expressions - you explicitly mark which methods need logging.

---

### **Q110: What is the @DeclareParents annotation in AOP?**

`@DeclareParents` is used for introduction - adding new interfaces to existing classes.

This is an advanced feature rarely used in typical applications like PerformanceTrack. Here's a simple example:

```java
public interface Auditable {
    void setCreatedDate(LocalDateTime date);
    LocalDateTime getCreatedDate();
}

@Aspect
@Component
public class AuditableAspect {
    
    @DeclareParents(
        value = "com.performancetrack.model.*",
        defaultImpl = AuditableImpl.class
    )
    public static Auditable auditable;
}
```

Now all model classes automatically implement `Auditable` interface without changing their code.

For PerformanceTrack, you won't need this. It's mentioned for interview awareness.

---

## **Advanced Spring & Spring Boot Questions**

### **Q111: What is @Transactional annotation and how does it work?**

`@Transactional` ensures that a group of database operations either all succeed or all fail together.

In PerformanceTrack without `@Transactional`:
```java
public void createGoal(Goal goal) {
    goalRepository.save(goal);  // Saved to database
    
    // Exception occurs here
    throw new RuntimeException("Error!");
    
    auditRepository.save(audit);  // Never executes
}
```
Result: Goal is saved but audit is not - inconsistent data!

With `@Transactional`:
```java
@Transactional
public void createGoal(Goal goal) {
    goalRepository.save(goal);
    throw new RuntimeException("Error!");
    auditRepository.save(audit);
}
```
Result: Goal save is rolled back - database remains consistent!

Place `@Transactional` on service methods that perform multiple database operations. Spring handles begin transaction, commit (if success), rollback (if exception).

---

### **Q112: What are different transaction propagation levels?**

Propagation defines how transactions relate when one transactional method calls another.

**REQUIRED (default)**: Use existing transaction or create new one
```java
@Transactional(propagation = Propagation.REQUIRED)
public void methodA() {
    methodB();  // Uses same transaction
}
```

**REQUIRES_NEW**: Always create new transaction, suspend existing one
```java
@Transactional(propagation = Propagation.REQUIRES_NEW)
public void methodB() {
    // Runs in separate transaction
    // If this fails, methodA's transaction is not affected
}
```

**SUPPORTS**: Use transaction if exists, otherwise run without transaction
```java
@Transactional(propagation = Propagation.SUPPORTS)
public void methodC() {
    // Will join transaction if called from transactional method
    // Will run without transaction if called from non-transactional method
}
```

**MANDATORY**: Must run in existing transaction, throws exception if none exists

**NEVER**: Must run without transaction, throws exception if transaction exists

**NOT_SUPPORTED**: Suspend existing transaction and run without transaction

For PerformanceTrack, use default **REQUIRED** for most cases. Use **REQUIRES_NEW** when you need independent transactions (like logging - you want logs saved even if main transaction fails).

---

### **Q113: What are different transaction isolation levels?**

Isolation defines how transaction changes are visible to other transactions.

**READ_UNCOMMITTED**: Can read uncommitted changes (dirty reads)
- Fastest but least safe

**READ_COMMITTED**: Can only read committed changes
- Most common in production

**REPEATABLE_READ**: Same query returns same result within transaction
- Prevents non-repeatable reads

**SERIALIZABLE**: Complete isolation, transactions run one after another
- Safest but slowest

In PerformanceTrack:
```java
@Transactional(isolation = Isolation.READ_COMMITTED)
public void processReview(Long reviewId) {
    Review review = reviewRepository.findById(reviewId);
    // Only sees committed changes by other transactions
}
```

Default isolation level is database-dependent (MySQL uses REPEATABLE_READ, PostgreSQL uses READ_COMMITTED). For most applications including PerformanceTrack, default is fine. Change only if you face specific concurrency issues.

---

### **Q114: What is lazy loading vs eager loading in JPA?**

Controls when related entities are fetched from database.

**Lazy Loading (default for collections):** Fetch related data only when accessed
```java
@Entity
public class Employee {
    @OneToMany(fetch = FetchType.LAZY)  // Default for OneToMany
    private List<Goal> goals;
}

Employee emp = employeeRepository.findById(1);  // Fetches only employee
System.out.println(emp.getName());  // Works fine
System.out.println(emp.getGoals().size());  // NOW fetches goals from database
```

**Eager Loading (default for single entities):** Fetch related data immediately
```java
@Entity
public class Employee {
    @OneToMany(fetch = FetchType.EAGER)
    private List<Goal> goals;
}

Employee emp = employeeRepository.findById(1);  // Fetches employee AND goals
```

**Problem with lazy loading:** If Hibernate session is closed, accessing lazy-loaded data throws `LazyInitializationException`.

**Problem with eager loading:** Fetches unnecessary data, slows down queries.

In PerformanceTrack, use lazy loading (default) for collections. Fetch eagerly only when you always need the related data.

---

### **Q115: What is N+1 query problem and how to solve it?**

N+1 problem occurs when you fetch N entities, then execute 1 query for each entity to fetch related data.

In PerformanceTrack:
```java
List<Employee> employees = employeeRepository.findAll();  // 1 query

for (Employee emp : employees) {
    emp.getGoals().size();  // N queries (one per employee)
}
```

If you have 100 employees, this executes 101 queries! (1 for employees + 100 for goals)

**Solution 1: Use JOIN FETCH**
```java
@Query("SELECT e FROM Employee e JOIN FETCH e.goals")
List<Employee> findAllWithGoals();
```
Single query fetches everything.

**Solution 2: Use @EntityGraph**
```java
@EntityGraph(attributePaths = {"goals"})
List<Employee> findAll();
```

**Solution 3: Use Batch fetching**
```java
@Entity
public class Employee {
    @OneToMany(fetch = FetchType.LAZY)
    @BatchSize(size = 10)
    private List<Goal> goals;
}
```
Fetches goals in batches of 10 instead of one by one.

For PerformanceTrack reports where you need employee data with goals, use JOIN FETCH to avoid N+1 problem.

---

### **Q116: What is the difference between @Component, @Repository, @Service, and @Controller in terms of functionality?**

Functionally, they all create Spring beans. The difference is semantic and some special features:

**@Component:** Generic stereotype, no special features
```java
@Component
public class EmailValidator { }
```

**@Repository:** For data access layer, adds exception translation
```java
@Repository
public interface ReportRepository extends JpaRepository<Report, Long> { }
```
Database exceptions (like `SQLException`) are automatically translated to Spring's `DataAccessException`.

**@Service:** For business logic layer, no special features (just semantic)
```java
@Service
public class ReportService { }
```

**@Controller:** For web layer, enables request handling
```java
@Controller
public class ViewController { }
```

**@RestController:** Controller + ResponseBody
```java
@RestController
public class ApiController { }
```

In PerformanceTrack:
- Use `@Repository` for repository interfaces
- Use `@Service` for service classes
- Use `@RestController` for REST controllers
- Use `@Component` for utility classes

This makes code self-documenting - anyone can see which layer a class belongs to.

---

### **Q117: What is the difference between @Bean and @Component?**

**@Component:** Class-level, for component scanning
```java
@Component
public class EmailService {
    // Spring scans and creates bean automatically
}
```

**@Bean:** Method-level, for manual bean creation
```java
@Configuration
public class AppConfig {
    
    @Bean
    public EmailService emailService() {
        EmailService service = new EmailService();
        service.setSmtpServer("smtp.gmail.com");  // Custom configuration
        return service;
    }
}
```

Use **@Component** for your own classes (simple, automatic).
Use **@Bean** when:
- Third-party classes (can't add @Component)
- Need custom initialization logic
- Conditional bean creation

In PerformanceTrack, most beans use `@Component/@Service/@Repository`. Use `@Bean` for things like password encoder, external library configurations.

---

### **Q118: What is Spring Bean scope and what are different scopes?**

Scope defines lifecycle and visibility of beans.

**Singleton (default):** One instance per Spring container
```java
@Service  // Default singleton
public class ReportService { }
```
All autowired places get same instance.

**Prototype:** New instance every time it's requested
```java
@Service
@Scope("prototype")
public class ReportGenerator { }
```
Each autowire gets different instance.

**Request (Web applications):** One instance per HTTP request
```java
@Component
@Scope(value = WebApplicationContext.SCOPE_REQUEST, proxyMode = ScopedProxyMode.TARGET_CLASS)
public class RequestContext { }
```

**Session (Web applications):** One instance per HTTP session

**Application (Web applications):** One instance per ServletContext

**WebSocket (Web applications):** One instance per WebSocket session

For PerformanceTrack REST API, use **singleton** (default) for stateless beans like services and repositories. Use **prototype** only if bean maintains state that shouldn't be shared.

---

### **Q119: What is the difference between constructor injection, setter injection, and field injection?**

Three ways to inject dependencies:

**Field Injection (simplest, most common):**
```java
@RestController
public class ReportController {
    @Autowired
    private ReportService reportService;
}
```
Pros: Clean, concise
Cons: Harder to test, hides dependencies

**Constructor Injection (recommended):**
```java
@RestController
public class ReportController {
    
    private final ReportService reportService;
    
    @Autowired  // Optional in Spring 4.3+
    public ReportController(ReportService reportService) {
        this.reportService = reportService;
    }
}
```
Pros: Immutable, easy to test, explicit dependencies
Cons: More verbose

**Setter Injection (rarely used):**
```java
@RestController
public class ReportController {
    
    private ReportService reportService;
    
    @Autowired
    public void setReportService(ReportService reportService) {
        this.reportService = reportService;
    }
}
```
Pros: Optional dependencies
Cons: Mutable, verbose

For PerformanceTrack, field injection is fine for learning. In production code, teams prefer constructor injection for better testability and immutability.

---

### **Q120: What is circular dependency and how to resolve it?**

Circular dependency occurs when beans depend on each other in a circle.

```java
@Service
public class ServiceA {
    @Autowired
    private ServiceB serviceB;  // A depends on B
}

@Service
public class ServiceB {
    @Autowired
    private ServiceA serviceA;  // B depends on A
}
```

Spring throws `BeanCurrentlyInCreationException` because it can't create A without B, and can't create B without A.

**Solutions:**

**1. Redesign (best):** Fix the design - circular dependency usually indicates bad architecture
```java
@Service
public class ServiceC { }  // Extract common logic

@Service
public class ServiceA {
    @Autowired
    private ServiceC serviceC;
}

@Service
public class ServiceB {
    @Autowired
    private ServiceC serviceC;
}
```

**2. Use @Lazy:**
```java
@Service
public class ServiceA {
    @Autowired
    @Lazy  // Creates proxy, delays actual initialization
    private ServiceB serviceB;
}
```

**3. Use setter injection:**
```java
@Service
public class ServiceA {
    private ServiceB serviceB;
    
    @Autowired
    public void setServiceB(ServiceB serviceB) {
        this.serviceB = serviceB;
    }
}
```

For PerformanceTrack, avoid circular dependencies by proper layering - controllers depend on services, services depend on repositories, never the reverse.

---

**Should I continue with more advanced topics? I can cover:**
- Spring Security basics
- REST API documentation (Swagger)
- Caching
- Scheduling
- Async processing
- Spring Data JPA advanced queries
- Testing in Spring Boot

Let me know if you want me to continue!