# Complete Interview Questions Guide - Part 1

I'll cover all these topics comprehensively. Let me start with **Git, Logger, and Lombok** in this response.

---

## **GIT INTERVIEW QUESTIONS**

### **Basic Git Concepts**

**Q1: What is Git and why do we use it?**

Git is a version control system that tracks changes in your code. Think of it like a save game feature in video games - you can save your progress at different points and go back if something breaks.

**Example:** In your PerformanceTrack project, if someone accidentally deletes the ReportController.java file, Git lets you restore it from the last saved version.

---

**Q2: What is the difference between Git and GitHub?**

Git is the tool that runs on your computer to track changes. GitHub is a website where you store your Git projects online so your team can access them.

**Example:** Git is like Microsoft Word on your computer, GitHub is like Google Drive where you save Word documents to share with others.

---

**Q3: What is a repository in Git?**

A repository (or repo) is a folder that Git is tracking. It contains all your project files plus a hidden .git folder that stores all the version history.

**Example:** Your PerformanceTrack folder becomes a repository when you run `git init` inside it.

---

**Q4: What is a commit in Git?**

A commit is like taking a snapshot of your project at a specific moment. It saves the current state of all your files with a message describing what you changed.

**Example:** After fixing a bug in ReportService.java, you commit with message "Fixed null pointer exception in calculateAverageScore method".

---

**Q5: What is the staging area in Git?**

The staging area is like a preparation zone where you select which changes you want to include in your next commit. Not all changes have to be committed together.

**Example:** You modified ReportController.java and Report.java, but only ReportController.java is ready. You stage only that file using `git add ReportController.java`.

---

**Q6: What is a branch in Git?**

A branch is a separate line of development. It's like creating a parallel universe where you can make changes without affecting the main code.

**Example:** You create a branch called "feature-analytics-dashboard" to build new analytics features. Meanwhile, your teammate works on the main branch fixing bugs.

---

**Q7: What is the master/main branch?**

The master (or main) branch is the default primary branch in Git. It usually contains the stable, production-ready code.

**Example:** In PerformanceTrack, your main branch has the working version deployed to your demo environment. New features are developed in other branches first.

---

**Q8: What is merging in Git?**

Merging combines changes from one branch into another. It's like combining two different versions of a document into one.

**Example:** After completing the analytics dashboard in your feature branch, you merge it back to the main branch so everyone has access to the new feature.

---

**Q9: What is a merge conflict?**

A merge conflict happens when Git can't automatically combine changes because two people modified the same lines of code differently.

**Example:** You changed line 50 in ReportService.java to calculate scores differently, but your teammate also changed the same line. Git asks you to manually decide which version to keep.

---

**Q10: What is git clone?**

Git clone creates a copy of a remote repository on your local machine. It downloads all files and complete history.

**Example:** When a new teammate joins PerformanceTrack project, they run `git clone <repo-url>` to get the entire project on their laptop.

---

**Q11: What is git pull?**

Git pull downloads the latest changes from the remote repository and merges them into your current branch.

**Example:** Your teammate pushed updates to ReportController.java. You run `git pull` to get those changes on your computer.

---

**Q12: What is git push?**

Git push uploads your local commits to the remote repository so others can see and use your changes.

**Example:** After committing your new analytics calculations, you run `git push` to send them to GitHub for your team to review.

---

**Q13: What is git fetch?**

Git fetch downloads changes from remote repository but doesn't merge them. It lets you see what others have done before deciding to merge.

**Example:** You run `git fetch` to see what changes your teammate made to the Analytics module without affecting your current work.

---

**Q14: What is the difference between git pull and git fetch?**

Git fetch only downloads changes without merging. Git pull downloads AND merges in one step.

**Example:** Use fetch when you want to review changes first. Use pull when you trust the changes and want them immediately in your code.

---

**Q15: What is git status?**

Git status shows the current state of your working directory - which files are modified, staged, or untracked.

**Example:** Before committing, you run `git status` to verify you're committing the right files from your Analytics module work.

---

**Q16: What is .gitignore file?**

.gitignore is a file where you list files/folders that Git should NOT track. Usually used for temporary files, compiled code, or sensitive data.

**Example:** In PerformanceTrack, your .gitignore includes `target/` folder (compiled code) and `application.properties` (database passwords).

---

**Q17: What is a remote repository?**

A remote repository is the version of your project hosted on a server (like GitHub). It's the central location where team members sync their work.

**Example:** Your PerformanceTrack project on GitHub is the remote repository. Your laptop has the local repository.

---

**Q18: What is git log?**

Git log shows the history of commits - who made what changes and when.

**Example:** You run `git log` to see all commits made to ReportService.java over the last month to understand how the analytics logic evolved.

---

**Q19: What is HEAD in Git?**

HEAD is a pointer to your current position in Git - usually pointing to the latest commit of your current branch.

**Example:** When you're on the main branch, HEAD points to the most recent commit on main. If you switch to feature-analytics branch, HEAD moves there.

---

**Q20: What is a detached HEAD state?**

Detached HEAD means you're looking at a specific commit directly, not at the tip of any branch. You're in "view only" mode.

**Example:** You run `git checkout abc123` to view an old version of ReportController.java from last week. Git warns you're in detached HEAD state.

---

### **Intermediate Git Questions**

**Q21: What is git rebase?**

Rebase moves your branch commits to start from a different point. It rewrites history to create a cleaner, linear commit sequence.

**Example:** Your feature-analytics branch started from main 10 commits ago. Main has 5 new commits now. Rebase moves your feature commits to start after those 5 new commits.

---

**Q22: What is the difference between merge and rebase?**

Merge preserves the complete history including when branches diverged. Rebase creates a linear history by moving commits.

**Example:** Merge is like saying "my feature branch split from main and joined back". Rebase is like saying "my feature was always built on top of the latest main".

---

**Q23: What is git stash?**

Git stash temporarily saves your uncommitted changes so you can work on something else, then restore those changes later.

**Example:** You're halfway through coding a new report feature when a critical bug appears. You run `git stash`, fix the bug, commit it, then run `git stash pop` to continue your report work.

---

**Q24: What is git reset?**

Git reset moves your branch pointer to a different commit, potentially discarding commits.

**Example:** You made 3 bad commits in ReportService.java. You run `git reset HEAD~3` to undo those commits (keeping the file changes), then redo them properly.

---

**Q25: What is the difference between git reset --soft, --mixed, and --hard?**

- **--soft**: Moves HEAD, keeps changes staged
- **--mixed** (default): Moves HEAD, unstages changes but keeps them in files  
- **--hard**: Moves HEAD, deletes all changes completely

**Example:** If you want to redo your last commit message, use `--soft`. If you want to completely throw away your last commit, use `--hard`.

---

**Q26: What is git revert?**

Git revert creates a new commit that undoes changes from a previous commit. Unlike reset, it doesn't erase history.

**Example:** You deployed a bad analytics calculation to production. Instead of deleting commits (dangerous), you run `git revert abc123` to create a new commit that undoes those changes.

---

**Q27: What is cherry-pick in Git?**

Cherry-pick copies a specific commit from one branch to another without merging the entire branch.

**Example:** You have 10 commits in feature-analytics branch, but only need the bug fix from commit abc123 in main branch. You cherry-pick that one commit.

---

**Q28: What is a tag in Git?**

A tag is a named reference to a specific commit, usually used to mark release versions.

**Example:** When PerformanceTrack v1.0 is ready for production, you create a tag called "v1.0" on that commit so you can always find the exact v1.0 code later.

---

**Q29: What is the difference between annotated and lightweight tags?**

Annotated tags store extra information (who created it, when, message). Lightweight tags are just simple pointers to commits.

**Example:** Use annotated tags for releases: `git tag -a v1.0 -m "First production release"`. Use lightweight tags for temporary markers.

---

**Q30: What is a fork in Git?**

A fork is your personal copy of someone else's repository. You can make changes in your fork without affecting the original project.

**Example:** You find a useful library on GitHub. You fork it to your account, add custom features for PerformanceTrack, while the original library remains unchanged.

---

**Q31: What is a pull request?**

A pull request asks the original repository owner to pull (merge) your changes from your fork or branch into their main code.

**Example:** After coding the analytics module, you create a pull request for your team lead to review your code before merging it to main branch.

---

**Q32: What is the difference between fork and clone?**

Fork creates a copy on GitHub (server-side). Clone creates a copy on your computer (local).

**Example:** You fork PerformanceTrack on GitHub to have your own copy there, then clone your fork to your laptop to write code.

---

**Q33: What is git diff?**

Git diff shows the differences between two versions of files - what lines were added, removed, or changed.

**Example:** Before committing, you run `git diff ReportService.java` to review exactly what changes you made to the analytics calculations.

---

**Q34: What is the difference between origin and upstream?**

Origin is your fork (your copy on GitHub). Upstream is the original repository you forked from.

**Example:** You forked PerformanceTrack from your college's official repo. Origin is your fork, upstream is the college's original repo.

---

**Q35: What is git blame?**

Git blame shows who last modified each line of a file and when.

**Example:** You find a bug in line 45 of ReportService.java. You run `git blame ReportService.java` to see which teammate wrote that line and ask them about it.

---

---

## **LOGGER (Logging Framework) INTERVIEW QUESTIONS**

### **Basic Logging Concepts**

**Q36: What is logging and why do we need it?**

Logging means recording what happens in your application while it runs. It's like keeping a diary of your application's activities.

**Example:** In PerformanceTrack, when a manager submits a performance review, the logger records "Manager ID 101 submitted review for employee ID 205 at 2:30 PM" so you can track this later if issues arise.

---

**Q37: What is a Logger in Java?**

A Logger is an object that writes log messages to various destinations like files, console, or databases.

**Example:** In ReportService.java, you create a logger: `Logger logger = LoggerFactory.getLogger(ReportService.class);` and use it to log when reports are generated.

---

**Q38: What are the different log levels?**

Common log levels from most to least severe: **ERROR > WARN > INFO > DEBUG > TRACE**

**Example:** 
- ERROR: "Failed to calculate performance score - database connection lost"
- WARN: "Report generation taking longer than usual - 15 seconds"
- INFO: "User admin successfully logged in"
- DEBUG: "Entering calculateAverageScore method with 5 review records"
- TRACE: "Variable reviewScore value changed from 0 to 85"

---

**Q39: What is the difference between System.out.println and Logger?**

System.out.println prints to console only and can't be controlled. Logger can write to multiple destinations, can be turned on/off, has severity levels, and includes timestamps automatically.

**Example:** In production, System.out.println messages disappear. Logger saves them to files so you can investigate issues that happened last night when no one was watching the console.

---

**Q40: What is SLF4J?**

SLF4J (Simple Logging Facade for Java) is an abstraction layer for various logging frameworks. It lets you write logging code once and switch logging implementations later without changing code.

**Example:** You write code using SLF4J in PerformanceTrack. Initially you use Logback, but later switch to Log4j2 without changing your ReportService.java code at all.

---

**Q41: What is Logback?**

Logback is a popular logging framework that actually writes the log messages. It's the successor to Log4j and works well with SLF4J.

**Example:** In PerformanceTrack, you use SLF4J in your code, but add Logback dependency in pom.xml to actually handle the logging operations.

---

**Q42: What is Log4j?**

Log4j is another popular logging framework (older than Logback). Log4j2 is its modern version with better performance.

**Example:** Some legacy corporate systems use Log4j. When you learn it, you can maintain those older applications.

---

**Q43: Why do we use SLF4J with Logback instead of using Logback directly?**

Using SLF4J (the interface) instead of Logback (the implementation) directly makes your code flexible. You can switch logging frameworks without changing your code.

**Example:** Your company decides to switch from Logback to Log4j2 for better performance. With SLF4J, you only change the dependency in pom.xml. Without SLF4J, you'd have to rewrite logging code in all 500 Java files.

---

**Q44: What is logback.xml configuration file?**

logback.xml is where you configure how and where logs should be written - to files, console, format, log levels, etc.

**Example:** In PerformanceTrack's logback.xml, you configure logs to write to both console during development and to a file at `/var/logs/performancetrack.log` in production.

---

**Q45: What is an Appender in logging?**

An Appender is the destination where log messages are written. Common types: ConsoleAppender (writes to screen), FileAppender (writes to file).

**Example:** You configure two appenders in PerformanceTrack - ConsoleAppender for developers to see logs during coding, and RollingFileAppender for production servers.

---

**Q46: What is a RollingFileAppender?**

RollingFileAppender creates new log files automatically when the current file gets too big or when a new day starts. Prevents one giant log file.

**Example:** PerformanceTrack creates a new log file every day: `performancetrack-2024-01-01.log`, `performancetrack-2024-01-02.log`, etc. Old logs older than 30 days get automatically deleted.

---

**Q47: What is a Logger name/category?**

Logger names organize logs hierarchically, usually matching your package/class names. This lets you control logging per class or package.

**Example:** You have loggers named `com.performancetrack.analytics.ReportService` and `com.performancetrack.analytics.ReportController`. You can set ReportService to DEBUG level while keeping ReportController at INFO level.

---

**Q48: What is the purpose of different log levels?**

Different levels help filter logs based on importance. In production, you might only want ERROR and WARN. In development, you want everything including DEBUG.

**Example:** Production shows only: "ERROR: Payment failed". Development shows: "DEBUG: Entering payment method", "DEBUG: Validating card", "ERROR: Payment failed", "DEBUG: Exiting payment method".

---

**Q49: When should you use ERROR log level?**

Use ERROR for serious problems that need immediate attention - the application can't complete an important operation.

**Example:** "ERROR: Failed to save performance review to database - review data lost for employee 205" - this is critical and someone needs to fix it immediately.

---

**Q50: When should you use WARN log level?**

Use WARN for potentially harmful situations that don't stop the application but might cause problems later.

**Example:** "WARN: Only 100MB disk space remaining for log files" or "WARN: Database connection pool 90% full - might run out soon".

---

**Q51: When should you use INFO log level?**

Use INFO for important business events and milestones that show normal application flow.

**Example:** "INFO: Performance review submitted successfully for employee 205", "INFO: Monthly analytics report generated for January 2024".

---

**Q52: When should you use DEBUG log level?**

Use DEBUG for detailed information useful during development and troubleshooting.

**Example:** "DEBUG: Executing SQL query: SELECT * FROM reviews WHERE employeeId = 205", "DEBUG: Calculated average score = 4.2 from 5 reviews".

---

**Q53: When should you use TRACE log level?**

Use TRACE for very detailed information, more granular than DEBUG. Usually only needed for deep troubleshooting.

**Example:** "TRACE: Loop iteration 1, processing review ID 501", "TRACE: Variable reviewScore = 4, adding to total = 12".

---

**Q54: What is MDC (Mapped Diagnostic Context)?**

MDC stores contextual information (like user ID, request ID) that automatically gets added to every log message in that thread.

**Example:** When user "manager101" logs in, you put their ID in MDC. Now every log message shows "manager101" without writing it manually each time: "INFO [manager101] - Viewing team reports".

---

**Q55: How do you create a Logger in a Java class?**

Using SLF4J, you create a logger as a static final variable at the top of your class.

**Example:**
```java
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

public class ReportService {
    private static final Logger logger = LoggerFactory.getLogger(ReportService.class);
    
    public void generateReport() {
        logger.info("Starting report generation");
    }
}
```

---

**Q56: What is the advantage of parameterized logging?**

Parameterized logging (using {} placeholders) is more efficient than string concatenation. The string is only built if that log level is enabled.

**Example:**
```java
// Bad - string concatenation happens even if INFO is disabled
logger.info("User " + userId + " scored " + score);

// Good - string only built if INFO is enabled
logger.info("User {} scored {}", userId, score);
```

---

**Q57: Should you check log level before logging?**

With modern logging frameworks and parameterized logging, you usually don't need to check. But for expensive operations, you might.

**Example:**
```java
// Usually no check needed
logger.debug("Score: {}", score);

// Check if debug enabled before expensive operation
if (logger.isDebugEnabled()) {
    logger.debug("Full report data: {}", generateExpensiveReport());
}
```

---

**Q58: What is log rotation?**

Log rotation means automatically creating new log files and archiving/deleting old ones based on size or time. Prevents disk space issues.

**Example:** PerformanceTrack rotates logs daily. Each day gets a new file. Files older than 30 days get deleted automatically. This prevents the log folder from growing infinitely.

---

**Q59: What information should a good log message contain?**

A good log message has: timestamp, log level, class name, clear description, and relevant data values.

**Example:** 
"2024-01-15 14:30:22 INFO [ReportService] - Performance report generated successfully for employee 205, score: 4.2, reviews counted: 5"

---

**Q60: What should you NOT log?**

Never log sensitive information like passwords, credit cards, personal identifying information (PII), or secret keys.

**Example:**
```java
// BAD - logging password
logger.info("User logged in with password: {}", password);

// GOOD - don't log password
logger.info("User {} logged in successfully", username);
```

---

---

## **LOMBOK INTERVIEW QUESTIONS**

### **Basic Lombok Concepts**

**Q61: What is Lombok?**

Lombok is a Java library that automatically generates boilerplate code like getters, setters, constructors, toString, equals, and hashCode methods using annotations.

**Example:** Instead of writing 50 lines of getter/setter methods in Report.java, you just add `@Data` annotation and Lombok generates them automatically.

---

**Q62: Why do we use Lombok?**

Lombok reduces code verbosity, makes classes cleaner and easier to read, and reduces chances of errors in repetitive code.

**Example:** Your Report.java class has 10 fields. Without Lombok, you write 10 getters + 10 setters + constructor + toString + equals + hashCode = 100+ lines. With Lombok's `@Data`, it's just 1 line.

---

**Q63: How do you add Lombok to a Spring Boot project?**

Add Lombok dependency in pom.xml and install Lombok plugin in your IDE (IntelliJ or Eclipse).

**Example:**
```xml
<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
    <scope>provided</scope>
</dependency>
```

---

**Q64: What is @Getter annotation?**

@Getter generates getter methods for all fields in a class or for a specific field.

**Example:**
```java
@Getter
public class Report {
    private Long id;
    private String title;
}
// Lombok generates: getId(), getTitle()
```

---

**Q65: What is @Setter annotation?**

@Setter generates setter methods for all fields in a class or for a specific field.

**Example:**
```java
@Setter
public class Report {
    private Long id;
    private String title;
}
// Lombok generates: setId(Long id), setTitle(String title)
```

---

**Q66: What is @Data annotation?**

@Data is a convenient annotation that combines @Getter, @Setter, @ToString, @EqualsAndHashCode, and @RequiredArgsConstructor.

**Example:**
```java
@Data
public class Report {
    private Long id;
    private String title;
}
// Lombok generates all getters, setters, toString, equals, hashCode
```

---

**Q67: What is @NoArgsConstructor?**

@NoArgsConstructor generates a constructor with no parameters (empty constructor).

**Example:**
```java
@NoArgsConstructor
public class Report {
    private Long id;
    private String title;
}
// Lombok generates: public Report() {}
```

---

**Q68: What is @AllArgsConstructor?**

@AllArgsConstructor generates a constructor with parameters for all fields in the class.

**Example:**
```java
@AllArgsConstructor
public class Report {
    private Long id;
    private String title;
}
// Lombok generates: public Report(Long id, String title) {...}
```

---

**Q69: What is @RequiredArgsConstructor?**

@RequiredArgsConstructor generates a constructor for all final fields and fields marked with @NonNull.

**Example:**
```java
@RequiredArgsConstructor
public class ReportService {
    private final ReportRepository repository; // final field
    private final Logger logger; // final field
}
// Lombok generates: public ReportService(ReportRepository repository, Logger logger) {...}
```

---

**Q70: What is @ToString annotation?**

@ToString generates a toString() method that returns a string representation of the object showing all field values.

**Example:**
```java
@ToString
public class Report {
    private Long id;
    private String title;
}
// Lombok generates toString: "Report(id=1, title=Q1 Analytics)"
```

---

**Q71: What is @EqualsAndHashCode?**

@EqualsAndHashCode generates equals() and hashCode() methods based on the fields in your class.

**Example:**
```java
@EqualsAndHashCode
public class Report {
    private Long id;
    private String title;
}
// Lombok generates equals() and hashCode() using id and title fields
```

---

**Q72: What is @Builder annotation?**

@Builder implements the Builder pattern, allowing you to create objects using a fluent API.

**Example:**
```java
@Builder
public class Report {
    private Long id;
    private String title;
}

// Usage:
Report report = Report.builder()
    .id(1L)
    .title("Q1 Analytics")
    .build();
```

---

**Q73: What is @Slf4j annotation?**

@Slf4j automatically creates a logger field named 'log' in your class using SLF4J.

**Example:**
```java
@Slf4j
public class ReportService {
    public void generateReport() {
        log.info("Generating report"); // no need to create logger manually
    }
}
// Lombok generates: private static final Logger log = LoggerFactory.getLogger(ReportService.class);
```

---

**Q74: What is @NonNull annotation?**

@NonNull generates null-check code that throws NullPointerException if the parameter is null.

**Example:**
```java
public class ReportService {
    public void processReport(@NonNull Report report) {
        // Lombok adds: if (report == null) throw new NullPointerException("report is marked non-null but is null");
    }
}
```

---

**Q75: What is @Value annotation?**

@Value makes a class immutable - all fields become final, only getters are generated (no setters), and class becomes final.

**Example:**
```java
@Value
public class ReportConfig {
    String format;
    int maxRows;
}
// Cannot change format or maxRows after object creation
```

---

**Q76: What is the difference between @Data and @Value?**

@Data creates mutable objects (with setters). @Value creates immutable objects (no setters, fields are final).

**Example:** Use @Data for Report entity that can be modified. Use @Value for ReportConfig that should never change after creation.

---

**Q77: What is @Cleanup annotation?**

@Cleanup ensures that resources (like file streams, database connections) are automatically closed, similar to try-with-resources.

**Example:**
```java
@Cleanup InputStream in = new FileInputStream("report.txt");
// Lombok ensures in.close() is called automatically
```

---

**Q78: Can you exclude certain fields from @ToString?**

Yes, using @ToString(exclude = {"fieldName"}) or @ToString.Exclude on specific fields.

**Example:**
```java
@ToString
public class User {
    private String username;
    @ToString.Exclude
    private String password; // won't appear in toString()
}
```

---

**Q79: Can you exclude certain fields from @EqualsAndHashCode?**

Yes, using @EqualsAndHashCode(exclude = {"fieldName"}) or @EqualsAndHashCode.Exclude on specific fields.

**Example:**
```java
@EqualsAndHashCode
public class Report {
    private Long id;
    @EqualsAndHashCode.Exclude
    private Date createdDate; // not used in equals/hashCode
}
```

---

**Q80: What is AccessLevel in Lombok?**

AccessLevel controls the visibility (public, private, protected, package-private) of generated methods.

**Example:**
```java
@Getter(AccessLevel.PRIVATE)
public class Report {
    private String internalCode; // only private getInternalCode() generated
}
```

---

**Q81: What is @With annotation?**

@With generates "wither" methods that create a copy of an object with one field changed. Useful for immutable objects.

**Example:**
```java
@With
@Value
public class Report {
    Long id;
    String title;
}

Report r1 = new Report(1L, "Old Title");
Report r2 = r1.withTitle("New Title"); // creates new object, r1 unchanged
```

---

**Q82: What problem does Lombok solve in JPA entities?**

Lombok reduces boilerplate in JPA entities. However, be careful with @Data on entities - it can cause issues with lazy loading and circular references.

**Example:**
```java
@Entity
@Getter @Setter  // Better than @Data for entities
@NoArgsConstructor
public class Report {
    @Id
    private Long id;
    private String title;
}
```

---

**Q83: Why should you avoid @Data on JPA entities with relationships?**

@Data includes @EqualsAndHashCode and @ToString which process all fields including lazy-loaded relationships. This can trigger unnecessary database queries or cause infinite loops in bidirectional relationships.

**Example:** Instead of @Data, use @Getter @Setter @NoArgsConstructor for entities with @OneToMany or @ManyToOne relationships.

---

**Q84: What is @FieldDefaults annotation?**

@FieldDefaults sets default modifiers for all fields in a class, commonly used to make all fields private and final.

**Example:**
```java
@FieldDefaults(level = AccessLevel.PRIVATE, makeFinal = true)
public class ReportConfig {
    String format;  // becomes: private final String format;
    int maxRows;    // becomes: private final int maxRows;
}
```

---

**Q85: What happens at compile time when you use Lombok?**

Lombok plugs into the Java compiler and modifies the bytecode to add the generated methods during compilation. Your .class files contain all the generated code.

**Example:** You write 10 lines with Lombok annotations. Compiler produces .class file with 100 lines including all generated getters, setters, etc.

---

I'll continue with **Validation, Agile, and Design Patterns** in the next response. Should I continue?

# Complete Interview Questions Guide - Part 2

Continuing with **Validation, Agile, and Design Patterns**...

---

## **VALIDATION INTERVIEW QUESTIONS**

### **Basic Validation Concepts**

**Q86: What is validation in Spring Boot?**

Validation is the process of checking if data meets certain rules before processing it. It ensures users don't send bad data to your application.

**Example:** In PerformanceTrack, when a manager submits a performance review, validation checks that the score is between 1-5, the comment is not empty, and the employee ID actually exists.

---

**Q87: Why do we need validation?**

Validation prevents bad data from entering your system, protects against security issues, ensures data consistency, and gives users clear error messages.

**Example:** Without validation, someone could submit a performance score of 9999 or -5, which would break your analytics calculations and corrupt your database.

---

**Q88: What is Bean Validation API?**

Bean Validation API (JSR-303/JSR-380) is a Java standard for validating objects using annotations. Spring Boot uses it for automatic validation.

**Example:** You add `@NotNull` annotation on a field, and Spring Boot automatically checks if that field has a value when data comes from the API.

---

**Q89: What dependency do you need for validation in Spring Boot?**

Spring Boot Starter Validation provides all validation features.

**Example:**
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-validation</artifactId>
</dependency>
```

---

**Q90: What is @Valid annotation?**

@Valid tells Spring Boot to validate an object before passing it to your method. Used in controller methods.

**Example:**
```java
@PostMapping("/reviews")
public void createReview(@Valid @RequestBody Review review) {
    // Spring validates review object before this code runs
}
```

---

**Q91: What is @Validated annotation?**

@Validated is Spring's version of @Valid with extra features like validation groups. Can be used at class level or method level.

**Example:**
```java
@Validated
@RestController
public class ReviewController {
    // Enables validation for all methods in this controller
}
```

---

**Q92: What is @NotNull annotation?**

@NotNull checks that a field is not null. It can be null during object creation but must have a value when validated.

**Example:**
```java
public class Review {
    @NotNull(message = "Employee ID cannot be null")
    private Long employeeId;
}
```

---

**Q93: What is @NotEmpty annotation?**

@NotEmpty checks that a String, Collection, Map, or Array is not null AND not empty (has at least one element).

**Example:**
```java
public class Review {
    @NotEmpty(message = "Review comments cannot be empty")
    private String comments;
}
```

---

**Q94: What is @NotBlank annotation?**

@NotBlank is for Strings only. It checks the string is not null, not empty, and not just whitespace.

**Example:**
```java
public class Review {
    @NotBlank(message = "Review title is required")
    private String title; // "   " (only spaces) would fail validation
}
```

---

**Q95: What is the difference between @NotNull, @NotEmpty, and @NotBlank?**

- @NotNull: Value must not be null (but can be empty)
- @NotEmpty: Must not be null AND must have length > 0 (but can be whitespace)
- @NotBlank: Must not be null, must have length > 0, AND must contain non-whitespace

**Example:**
- "": fails all three
- "   ": passes @NotNull and @NotEmpty, fails @NotBlank
- null: fails all three

---

**Q96: What is @Size annotation?**

@Size checks the length/size of Strings, Collections, Maps, or Arrays against min/max values.

**Example:**
```java
public class Review {
    @Size(min = 10, max = 500, message = "Comments must be between 10 and 500 characters")
    private String comments;
}
```

---

**Q97: What is @Min and @Max annotation?**

@Min and @Max check that a number is greater than or equal to min, or less than or equal to max.

**Example:**
```java
public class Review {
    @Min(value = 1, message = "Score must be at least 1")
    @Max(value = 5, message = "Score cannot exceed 5")
    private Integer score;
}
```

---

**Q98: What is @Email annotation?**

@Email checks if a string is a valid email format.

**Example:**
```java
public class User {
    @Email(message = "Please provide a valid email address")
    private String email; // accepts "user@company.com", rejects "usercompany.com"
}
```

---

**Q99: What is @Pattern annotation?**

@Pattern validates a string against a regular expression (regex) pattern.

**Example:**
```java
public class Employee {
    @Pattern(regexp = "^[0-9]{10}$", message = "Phone must be exactly 10 digits")
    private String phone;
}
```

---

**Q100: What is @Past and @Future annotation?**

@Past checks that a date is in the past. @Future checks that a date is in the future.

**Example:**
```java
public class Review {
    @Past(message = "Review period end date must be in the past")
    private LocalDate reviewPeriodEnd;
    
    @Future(message = "Next review date must be in the future")
    private LocalDate nextReviewDate;
}
```

---

**Q101: What is @DecimalMin and @DecimalMax?**

@DecimalMin and @DecimalMax validate decimal numbers (including BigDecimal and String representations of numbers).

**Example:**
```java
public class Review {
    @DecimalMin(value = "0.0", message = "Score cannot be negative")
    @DecimalMax(value = "5.0", message = "Score cannot exceed 5.0")
    private Double score;
}
```

---

**Q102: What is @Positive and @Negative?**

@Positive checks that a number is greater than 0. @Negative checks that a number is less than 0.

**Example:**
```java
public class Report {
    @Positive(message = "Report ID must be positive")
    private Long reportId;
}
```

---

**Q103: What is @PositiveOrZero and @NegativeOrZero?**

@PositiveOrZero checks number >= 0. @NegativeOrZero checks number <= 0.

**Example:**
```java
public class Review {
    @PositiveOrZero(message = "Score cannot be negative")
    private Integer score; // allows 0, 1, 2, 3... but not -1
}
```

---

**Q104: How do you customize validation error messages?**

You can provide custom messages in the annotation's message parameter.

**Example:**
```java
@NotBlank(message = "Employee name is required and cannot be blank")
@Size(min = 2, max = 50, message = "Employee name must be between 2 and 50 characters")
private String employeeName;
```

---

**Q105: What is BindingResult?**

BindingResult is an object that holds validation errors. It must come immediately after the @Valid parameter in your controller method.

**Example:**
```java
@PostMapping("/reviews")
public ResponseEntity createReview(@Valid @RequestBody Review review, BindingResult result) {
    if (result.hasErrors()) {
        // Handle validation errors
        return ResponseEntity.badRequest().body(result.getAllErrors());
    }
    return ResponseEntity.ok(reviewService.save(review));
}
```

---

**Q106: What happens if you don't use BindingResult?**

If validation fails and you don't have BindingResult, Spring throws MethodArgumentNotValidException and returns 400 Bad Request automatically.

**Example:** Without BindingResult, Spring automatically sends error response to client. With BindingResult, you control the error response format.

---

**Q107: How do you validate nested objects?**

Use @Valid on the nested object field to trigger validation of the nested object's fields.

**Example:**
```java
public class Review {
    @NotNull
    @Valid  // This validates the Employee object's fields
    private Employee employee;
}

public class Employee {
    @NotBlank
    private String name;
}
```

---

**Q108: How do you validate collections?**

Use @Valid on collection elements to validate each item in the list.

**Example:**
```java
public class PerformanceReviewBatch {
    @NotEmpty(message = "Must submit at least one review")
    @Valid  // Validates each Review in the list
    private List<Review> reviews;
}
```

---

**Q109: What is @AssertTrue and @AssertFalse?**

@AssertTrue checks that a boolean field/method returns true. @AssertFalse checks it returns false.

**Example:**
```java
public class Review {
    private boolean approved;
    
    @AssertTrue(message = "Review must be approved before submission")
    public boolean isApproved() {
        return approved;
    }
}
```

---

**Q110: Can you create custom validation annotations?**

Yes, you create a custom annotation and a validator class that implements the validation logic.

**Example:**
```java
@Constraint(validatedBy = FutureQuarterValidator.class)
@Target({ElementType.FIELD})
@Retention(RetentionPolicy.RUNTIME)
public @interface FutureQuarter {
    String message() default "Quarter must be in the future";
    Class<?>[] groups() default {};
    Class<? extends Payload>[] payload() default {};
}
```

---

**Q111: What is method-level validation?**

Method-level validation validates individual method parameters and return values, not just request bodies.

**Example:**
```java
@Validated
@Service
public class ReportService {
    public Report getReport(@NotNull @Positive Long reportId) {
        // reportId is validated before method executes
    }
}
```

---

**Q112: What is the difference between @Valid and @Validated for method-level validation?**

@Valid cannot be used at class level and doesn't support validation groups. @Validated can be used at class level and supports validation groups.

**Example:** Use @Validated on service classes for method parameter validation. Use @Valid on controller request bodies.

---

**Q113: What are validation groups?**

Validation groups let you apply different validation rules in different scenarios (like onCreate vs onUpdate).

**Example:**
```java
public class Review {
    @Null(groups = OnCreate.class)
    @NotNull(groups = OnUpdate.class)
    private Long id; // id must be null on create, must exist on update
}

public interface OnCreate {}
public interface OnUpdate {}
```

---

**Q114: How do you handle validation errors globally?**

Use @ControllerAdvice with @ExceptionHandler to handle validation exceptions across all controllers.

**Example:**
```java
@ControllerAdvice
public class GlobalExceptionHandler {
    @ExceptionHandler(MethodArgumentNotValidException.class)
    public ResponseEntity handleValidationErrors(MethodArgumentNotValidException ex) {
        List<String> errors = ex.getBindingResult()
            .getAllErrors()
            .stream()
            .map(ObjectError::getDefaultMessage)
            .collect(Collectors.toList());
        return ResponseEntity.badRequest().body(errors);
    }
}
```

---

**Q115: What is the order of validation execution?**

Validation happens in this order: Field-level annotations → Class-level annotations → Custom validators.

**Example:** @NotNull is checked first, then @Size, then your custom @ValidReviewScore annotation.

---

---

## **AGILE INTERVIEW QUESTIONS**

### **Basic Agile Concepts**

**Q116: What is Agile?**

Agile is a project management approach where you build software in small increments, get feedback quickly, and adapt based on what you learn.

**Example:** Instead of spending 6 months building all of PerformanceTrack and then showing it to users, you build the Goal Setting module in 2 weeks, show it to managers, get feedback, improve it, then move to Reviews module.

---

**Q117: What is the Agile Manifesto?**

The Agile Manifesto lists 4 core values: Individuals and interactions over processes and tools, Working software over comprehensive documentation, Customer collaboration over contract negotiation, Responding to change over following a plan.

**Example:** In PerformanceTrack project, when your team lead suggests changing the review scoring from 1-5 to 1-10 scale mid-project, Agile says adapt to this change rather than saying "but the plan says 1-5".

---

**Q118: What is a Sprint?**

A Sprint is a fixed time period (usually 1-4 weeks) where the team commits to completing specific features. Each sprint produces working software.

**Example:** In your PerformanceTrack project, you might have 2-week sprints. Sprint 1: build basic goal creation. Sprint 2: add goal approval workflow. Each sprint delivers working features.

---

**Q119: What is a User Story?**

A User Story describes a feature from the user's perspective, usually in format: "As a [user type], I want [goal], so that [benefit]".

**Example:** "As a manager, I want to submit performance reviews for my team members, so that I can provide formal feedback on their work."

---

**Q120: What are Story Points?**

Story Points measure the effort/complexity of a user story. They're relative numbers, not hours. Teams estimate effort using points (often 1, 2, 3, 5, 8, 13).

**Example:** Creating a simple "view employee list" feature might be 3 points. Building the complex "analytics dashboard with charts" might be 13 points. The ratio shows the dashboard is roughly 4x harder.

---

**Q121: What is Velocity?**

Velocity is how many story points a team completes in a sprint. It helps predict how much work the team can handle.

**Example:** Your PerformanceTrack team completed 20 points in Sprint 1, 22 points in Sprint 2, 18 points in Sprint 3. Average velocity is 20 points. So you plan 20 points worth of work for Sprint 4.

---

**Q122: What is a Sprint Planning meeting?**

Sprint Planning is a meeting at the start of each sprint where the team decides what user stories to work on and how to build them.

**Example:** Your team meets Monday morning, reviews the backlog, picks 5 user stories totaling 20 points, and discusses how to implement the analytics module.

---

**Q123: What is a Daily Standup (Daily Scrum)?**

Daily Standup is a short daily meeting (15 minutes) where each team member shares: what they did yesterday, what they'll do today, and any blockers.

**Example:** You say: "Yesterday I completed the ReportService logic. Today I'll work on ReportController. I'm blocked waiting for database credentials from the admin."

---

**Q124: What is a Sprint Review (Demo)?**

Sprint Review is a meeting at sprint end where the team demonstrates completed features to stakeholders and gets feedback.

**Example:** After Sprint 1, you demo the goal-setting feature to your college project guide, showing how managers can create goals for employees.

---

**Q125: What is a Sprint Retrospective?**

Sprint Retrospective is a meeting after each sprint where the team discusses what went well, what didn't, and how to improve.

**Example:** Your team discusses: "Good: we finished all planned stories. Bad: too many merge conflicts. Improvement: let's communicate before working on the same files."

---

**Q126: What is a Product Backlog?**

Product Backlog is a prioritized list of all features, enhancements, and fixes that might be built for the product.

**Example:** PerformanceTrack backlog includes: Goal Setting (high priority), Reviews (high), Analytics (medium), Notifications (low), Mobile App (low).

---

**Q127: What is a Sprint Backlog?**

Sprint Backlog is the specific set of user stories the team commits to completing in the current sprint.

**Example:** For Sprint 1, from the full product backlog, you selected just 5 stories related to basic goal creation to focus on for the next 2 weeks.

---

**Q128: What is a Product Owner?**

Product Owner is the person who represents stakeholders, prioritizes features, and decides what goes in each sprint.

**Example:** In PerformanceTrack, your team lead or college professor might act as Product Owner, deciding whether to build analytics or notifications first.

---

**Q129: What is a Scrum Master?**

Scrum Master facilitates Agile processes, removes blockers for the team, and ensures the team follows Scrum practices.

**Example:** In your college project, one team member might be Scrum Master - they organize standup meetings, track sprint progress, and help when someone is stuck.

---

**Q130: What is the Development Team in Agile?**

Development Team includes all the people who build the product - developers, testers, designers working together.

**Example:** Your PerformanceTrack team of 6 people is the development team - some code, some test, some design UI, all working together.

---

**Q131: What is Definition of Done (DoD)?**

Definition of Done is a checklist that defines when a user story is truly complete.

**Example:** Your DoD might include: Code written, Unit tests passed, Code reviewed, Tested manually, Documentation updated, Merged to main branch.

---

**Q132: What is an Epic?**

An Epic is a large user story that's too big for one sprint, so it gets broken into smaller user stories.

**Example:** Epic: "Performance Review System". This breaks into smaller stories: "Create review form", "Submit review", "Manager approval", "View review history".

---

**Q133: What is a Spike?**

A Spike is a time-boxed research task to investigate something unknown before implementing a feature.

**Example:** Before building the analytics module, you do a 2-day spike to research which charting library (Chart.js vs D3.js) works best with Spring Boot.

---

**Q134: What is Backlog Refinement (Grooming)?**

Backlog Refinement is ongoing activity where the team reviews, updates, and estimates upcoming user stories.

**Example:** Mid-sprint, your team spends 1 hour reviewing next sprint's stories, breaking down complex ones, and estimating story points.

---

**Q135: What is the difference between Scrum and Agile?**

Agile is the overall philosophy. Scrum is a specific framework for implementing Agile with defined roles, events, and artifacts.

**Example:** Agile is like saying "play music". Scrum is like saying "play music using these specific instruments, in this time signature, with these roles".

---

**Q136: What is Kanban?**

Kanban is another Agile framework that uses a visual board to track work through stages (To Do, In Progress, Done) without fixed sprints.

**Example:** Instead of 2-week sprints, your team uses a Kanban board where stories flow continuously from "Backlog" → "Development" → "Testing" → "Done".

---

**Q137: What is the difference between Scrum and Kanban?**

Scrum uses fixed-length sprints with specific roles and ceremonies. Kanban is continuous flow with no sprints, focusing on limiting work in progress.

**Example:** Scrum: plan work every 2 weeks, deliver at sprint end. Kanban: pick next priority task whenever you finish current task, deliver continuously.

---

**Q138: What is WIP (Work In Progress) limit?**

WIP limit restricts how many tasks can be in a stage at once. Prevents team from starting too many things without finishing them.

**Example:** Your Kanban board has WIP limit of 3 for "In Development". Team can't start a 4th task until one of the 3 moves to testing.

---

**Q139: What is Burndown Chart?**

Burndown Chart shows remaining work (in story points or hours) over time during a sprint. Ideally slopes downward.

**Example:** Sprint starts with 30 points. Each day the chart shows: Day 1: 30 points, Day 3: 25 points, Day 5: 18 points, showing progress toward zero.

---

**Q140: What is a Burnup Chart?**

Burnup Chart shows completed work over time, going upward. Also shows scope changes.

**Example:** Chart shows completed points rising: Day 1: 0, Day 3: 5, Day 5: 12. Also shows if scope increased from 30 to 35 points mid-sprint.

---

**Q141: What is Time Boxing?**

Time Boxing means fixing a maximum time for an activity. Once time expires, you stop.

**Example:** Daily standup is time-boxed to 15 minutes. Even if not everyone spoke, meeting ends at 15 minutes to respect everyone's time.

---

**Q142: What is a Minimum Viable Product (MVP)?**

MVP is the simplest version of your product that delivers core value and can be released to users.

**Example:** PerformanceTrack MVP includes only: Create goals, Submit reviews, View reviews. Advanced analytics, notifications, mobile app come later.

---

**Q143: What is Continuous Integration (CI)?**

CI means frequently merging code to the main branch (daily or multiple times per day) and automatically running tests.

**Example:** Every time you push code to GitHub, automated tests run. If tests pass, code merges to main. Catches problems early.

---

**Q144: What is Continuous Deployment (CD)?**

CD means automatically deploying code to production after it passes all tests. Delivers features to users very quickly.

**Example:** When your PerformanceTrack code passes all tests, it automatically deploys to the demo server without manual intervention.

---

**Q145: What is Pair Programming?**

Pair Programming is when two developers work on the same code together - one types (driver), one reviews (navigator), and they switch roles.

**Example:** You and your teammate sit together. You code the ReportService while your teammate watches, suggests improvements, catches bugs. Then you switch.

---

**Q146: What is Code Review?**

Code Review is when another developer examines your code before it merges to main branch, checking for bugs, quality, and best practices.

**Example:** After you finish ReportController, you create a pull request. Your teammate reviews it, suggests renaming a variable, you fix it, then code merges.

---

**Q147: What is Technical Debt?**

Technical Debt is when you take shortcuts to deliver faster, but those shortcuts create problems later that need fixing.

**Example:** You skip writing unit tests for ReportService to finish the sprint on time. Later, this causes bugs and you spend 2 days fixing and adding tests - that's paying back technical debt.

---

**Q148: What is Refactoring?**

Refactoring means improving code structure and readability without changing its functionality.

**Example:** Your ReportService has a 200-line method. You refactor it into 5 smaller methods with clear names. The code does the same thing but is now easier to understand.

---

**Q149: What is Test-Driven Development (TDD)?**

TDD means writing tests BEFORE writing code. You write a failing test, then write code to make it pass.

**Example:** Before coding calculateAverageScore method, you write a test: "given 3 reviews with scores 4,5,3, average should be 4.0". Then you write the method to pass this test.

---

**Q150: What is Acceptance Criteria?**

Acceptance Criteria defines specific conditions that must be met for a user story to be considered complete.

**Example:**
User Story: "Manager can submit performance review"
Acceptance Criteria:
- Manager can rate employee from 1-5
- Comment field allows 500 characters
- Submit button saves to database
- Manager sees success message

---

---

## **DESIGN PATTERNS INTERVIEW QUESTIONS**

### **Basic Design Pattern Concepts**

**Q151: What are Design Patterns?**

Design Patterns are proven solutions to common programming problems. They're like blueprints you can customize to solve recurring design challenges.

**Example:** Instead of figuring out from scratch how to ensure only one database connection exists in PerformanceTrack, you use the Singleton pattern which solves this exact problem.

---

**Q152: Why do we use Design Patterns?**

Design Patterns provide tested solutions, make code more maintainable, help developers communicate better using common terminology, and prevent reinventing the wheel.

**Example:** When you say "I used Factory pattern for creating reports", your teammate immediately understands the structure without seeing code.

---

**Q153: What are the three main categories of Design Patterns?**

Three categories: **Creational** (how objects are created), **Structural** (how objects are composed), **Behavioral** (how objects communicate).

**Example:** Singleton (creational) controls object creation. Decorator (structural) adds features to objects. Observer (behavioral) notifies objects of changes.

---

### **Creational Design Patterns**

**Q154: What is Singleton Pattern?**

Singleton ensures a class has only ONE instance in the entire application and provides global access to it.

**Example:** In PerformanceTrack, you want only ONE database connection pool. Singleton pattern ensures no matter how many times you call DatabaseConnection.getInstance(), you get the same instance.

**Implementation:**
```java
public class DatabaseConnection {
    private static DatabaseConnection instance;
    
    private DatabaseConnection() {} // private constructor
    
    public static DatabaseConnection getInstance() {
        if (instance == null) {
            instance = new DatabaseConnection();
        }
        return instance;
    }
}
```

---

**Q155: Where is Singleton pattern used in Spring Boot?**

Spring Boot beans are singleton by default - Spring creates only one instance of each bean and reuses it.

**Example:** Your ReportService is created once by Spring. Every controller that needs ReportService gets the same instance, not a new one each time.

---

**Q156: What is Factory Pattern?**

Factory Pattern provides a method to create objects without specifying the exact class to create. The factory decides which class to instantiate.

**Example:** In PerformanceTrack, you have PDFReport, ExcelReport, HTMLReport. ReportFactory.createReport("PDF") returns appropriate report type based on the string parameter.

**Implementation:**
```java
public class ReportFactory {
    public static Report createReport(String type) {
        if (type.equals("PDF")) {
            return new PDFReport();
        } else if (type.equals("EXCEL")) {
            return new ExcelReport();
        }
        return new HTMLReport();
    }
}
```

---

**Q157: What is Builder Pattern?**

Builder Pattern constructs complex objects step by step. It's useful when an object has many optional parameters.

**Example:** Creating a Report object with many optional fields (title, description, format, filters, dateRange, includeCharts, etc.) is messy with constructors. Builder makes it clean.

**Implementation (using Lombok):**
```java
@Builder
public class Report {
    private String title;
    private String format;
    private LocalDate startDate;
    private LocalDate endDate;
    private boolean includeCharts;
}

// Usage:
Report report = Report.builder()
    .title("Q1 Performance")
    .format("PDF")
    .startDate(jan1)
    .includeCharts(true)
    .build();
```

---

**Q158: What is Prototype Pattern?**

Prototype Pattern creates new objects by copying an existing object (cloning) instead of creating from scratch.

**Example:** You have a complex Report configuration with 20 settings. Instead of setting all 20 again for a similar report, you clone the existing report and modify just 2 settings.

---

### **Structural Design Patterns**

**Q159: What is Adapter Pattern?**

Adapter Pattern converts one interface into another interface that clients expect. It's like a translator between incompatible interfaces.

**Example:** You're using an old ThirdPartyReportGenerator library that expects data in XML format, but your PerformanceTrack data is in JSON. You create ReportAdapter that converts JSON to XML before calling the library.

---

**Q160: What is Decorator Pattern?**

Decorator Pattern adds new functionality to objects dynamically without changing their structure. You wrap objects with decorator objects.

**Example:** You have a basicReport. You wrap it with EmailDecorator to add emailing capability, then wrap that with EncryptionDecorator to add encryption, without modifying basic report code.

---

**Q161: What is Proxy Pattern?**

Proxy Pattern provides a substitute/placeholder for another object to control access to it.

**Example:** In PerformanceTrack, you have SalaryReport. You create SalaryReportProxy that checks if the user is a manager before allowing access to actual salary data. Non-managers get denied.

---

**Q162: What is Facade Pattern?**

Facade Pattern provides a simple interface to a complex subsystem. It hides complexity behind a simple method.

**Example:** Generating a performance report requires: fetching goals from GoalService, reviews from ReviewService, calculating scores, formatting data, creating PDF. ReportFacade.generateReport() hides all this complexity behind one simple method.

**Implementation:**
```java
public class ReportFacade {
    private GoalService goalService;
    private ReviewService reviewService;
    private ScoreCalculator calculator;
    private PDFGenerator pdfGenerator;
    
    public Report generateReport(Long employeeId) {
        // Hides all complexity
        List<Goal> goals = goalService.getGoals(employeeId);
        List<Review> reviews = reviewService.getReviews(employeeId);
        double score = calculator.calculate(reviews);
        return pdfGenerator.generate(goals, reviews, score);
    }
}
```

---

**Q163: What is Composite Pattern?**

Composite Pattern treats individual objects and groups of objects uniformly using a tree structure.

**Example:** An organization has Employees and Managers. Managers are also employees but contain a list of subordinates. Composite pattern lets you calculate total salary for one employee OR a whole department using the same method.

---

### **Behavioral Design Patterns**

**Q164: What is Observer Pattern?**

Observer Pattern defines one-to-many dependency where when one object changes state, all dependent objects are notified automatically.

**Example:** When a performance review status changes to "Approved", the NotificationService, AuditService, and EmailService all get notified automatically without the review knowing about these services.

---

**Q165: Where is Observer pattern used in Spring Boot?**

Spring's Event mechanism uses Observer pattern. You publish an event, and multiple listeners can subscribe to it.

**Example:**
```java
// Publishing event
applicationEventPublisher.publishEvent(new ReviewApprovedEvent(review));

// Listening to event
@EventListener
public void handleReviewApproved(ReviewApprovedEvent event) {
    sendNotification(event.getReview());
}
```

---

**Q166: What is Strategy Pattern?**

Strategy Pattern defines a family of algorithms, encapsulates each one, and makes them interchangeable.

**Example:** PerformanceTrack calculates scores using different strategies: AverageStrategy (simple average), WeightedStrategy (recent reviews weighted more), ManagerOnlyStrategy (only manager reviews count). You can switch strategies at runtime.

**Implementation:**
```java
public interface ScoreStrategy {
    double calculate(List<Review> reviews);
}

public class AverageStrategy implements ScoreStrategy {
    public double calculate(List<Review> reviews) {
        // simple average logic
    }
}

public class WeightedStrategy implements ScoreStrategy {
    public double calculate(List<Review> reviews) {
        // weighted average logic
    }
}
```

---

**Q167: What is Template Method Pattern?**

Template Method defines the skeleton of an algorithm in a method, letting subclasses override specific steps without changing the structure.

**Example:** All reports follow same steps: fetchData → processData → formatData → generateOutput. ReportTemplate defines these steps. PDFReport and ExcelReport override formatData and generateOutput differently.

---

**Q168: What is Command Pattern?**

Command Pattern encapsulates a request as an object, allowing you to queue requests, log them, or undo them.

**Example:** User actions in PerformanceTrack (CreateGoalCommand, ApproveReviewCommand, DeleteGoalCommand) are objects. You can queue them, log them for audit, or implement undo functionality.

---

**Q169: What is State Pattern?**

State Pattern allows an object to change its behavior when its internal state changes.

**Example:** A Review has states: DRAFT, SUBMITTED, APPROVED, REJECTED. The Review object behaves differently in each state - in DRAFT you can edit, in SUBMITTED you can't edit but can approve, etc.

---

**Q170: What is Chain of Responsibility Pattern?**

Chain of Responsibility passes a request along a chain of handlers. Each handler decides to process the request or pass it to next handler.

**Example:** Review approval chain: First HR validates fields → Then Manager approves → Then Senior Manager final approval. Request moves through chain until someone handles or rejects it.

---

**Q171: What is Iterator Pattern?**

Iterator Pattern provides a way to access elements of a collection sequentially without exposing its underlying structure.

**Example:** Java's `for (Review review : reviews)` uses Iterator pattern. You don't need to know if reviews is an ArrayList, LinkedList, or Set.

---

**Q172: What is Dependency Injection (DI) pattern?**

Dependency Injection provides dependencies to a class from outside rather than the class creating them itself.

**Example:** Instead of ReportController creating its own ReportService (`new ReportService()`), Spring injects ReportService into the controller. This makes code more testable and flexible.

**Implementation:**
```java
@RestController
public class ReportController {
    private final ReportService reportService;
    
    @Autowired  // Spring injects this
    public ReportController(ReportService reportService) {
        this.reportService = reportService;
    }
}
```

---

**Q173: What is MVC (Model-View-Controller) Pattern?**

MVC separates application into three components: Model (data), View (UI), Controller (handles requests).

**Example:** In PerformanceTrack: Report.java is Model (data), HTML/JSP is View (UI), ReportController is Controller (handles HTTP requests). Each has separate responsibility.

---

**Q174: What is Repository Pattern?**

Repository Pattern abstracts data access logic, providing a collection-like interface to access domain objects.

**Example:** ReportRepository hides all database/SQL complexity. You call `reportRepository.findById(1)` without knowing if it's SQL, NoSQL, or file storage.

---

**Q175: What is DAO (Data Access Object) Pattern?**

DAO Pattern provides an abstract interface to database operations, separating business logic from data access logic.

**Example:** ReportDAO handles all database operations for reports (save, delete, find). ReportService uses ReportDAO but doesn't know SQL details.

---

**This completes all topics!** 

**Summary of what we covered:**
- **Git:** 35 questions (Q1-Q35)
- **Logger:** 25 questions (Q36-Q60)
- **Lombok:** 25 questions (Q61-Q85)
- **Validation:** 30 questions (Q86-Q115)
- **Agile:** 35 questions (Q116-Q150)
- **Design Patterns:** 25 questions (Q151-Q175)

**Total: 175 comprehensive interview questions** covering all your requested topics!

Would you like me to create a summary document of all these questions, or would you like me to dive deeper into any specific topic?