# Complete Interview Questions Guide: Spring Security, JWT, REST API

I'll provide you with comprehensive questions covering Spring Security, JWT authentication, and REST API best practices with conversational answers and PerformanceTrack examples.

---

## **SPRING SECURITY Interview Questions**

### **Q1: What is Spring Security?**

Spring Security is a framework that provides authentication, authorization, and protection against common security vulnerabilities for Spring applications.

Think of it as a security guard system for PerformanceTrack. Without Spring Security, anyone could access any API endpoint - employees could see admin reports, managers could delete reviews, etc.

With Spring Security:
- Only authenticated users can access the system (authentication)
- Users can only access what they're authorized for (authorization)
- Protection against attacks like CSRF, session fixation, etc.

In PerformanceTrack, Spring Security ensures that:
- Employees can view only their own goals
- Managers can approve reviews only for their team
- Admins can access all reports
- Nobody can access the system without logging in first

---

### **Q2: What is the difference between authentication and authorization?**

**Authentication:** Verifying WHO you are (proving identity)

Example in PerformanceTrack:
```
User: "I am John"
System: "Prove it - enter your password"
User: [enters password]
System: "Verified - you are John"
```

**Authorization:** Verifying WHAT you can do (checking permissions)

Example in PerformanceTrack:
```
John (authenticated): "I want to delete all reviews"
System: "Let me check your role... You are EMPLOYEE, not ADMIN"
System: "Access Denied - you cannot delete reviews"
```

Flow: First authenticate (login with username/password), then authorize (check if you have permission for specific action).

In real-world: Authentication is like showing your ID card at building entrance. Authorization is like checking if your ID allows you to enter the server room.

---

### **Q3: How does Spring Security work internally?**

Spring Security works through a chain of filters that intercept every HTTP request.

When a request comes to PerformanceTrack:
```
Request: GET /api/reports/123
    ↓
[Filter Chain starts]
    ↓
SecurityContextPersistenceFilter → Loads security context
    ↓
UsernamePasswordAuthenticationFilter → Checks username/password
    ↓
FilterSecurityInterceptor → Checks if user has permission
    ↓
[If all filters pass]
    ↓
Your Controller method executes
```

If any filter fails, request is rejected with 401 (Unauthorized) or 403 (Forbidden).

You configure these filters in `SecurityConfig` class. Spring Security automatically sets up the filter chain based on your configuration.

---

### **Q4: What is SecurityContext and SecurityContextHolder?**

**SecurityContext:** Stores security information about the current user (authentication details, roles, etc.)

**SecurityContextHolder:** A holder that stores SecurityContext using ThreadLocal, making it accessible anywhere in your application.

In PerformanceTrack:
```java
// After user logs in successfully, Spring Security stores their info
Authentication auth = SecurityContextHolder.getContext().getAuthentication();
String username = auth.getName();  // Gets currently logged-in username
Collection<GrantedAuthority> authorities = auth.getAuthorities();  // Gets roles
```

Example usage in ReportController:
```java
@GetMapping("/my-reports")
public List<Report> getMyReports() {
    String currentUser = SecurityContextHolder.getContext()
                                              .getAuthentication()
                                              .getName();
    return reportService.getReportsByEmployee(currentUser);
}
```

Think of SecurityContextHolder as a global variable that holds information about who is currently making the request. Each thread (each HTTP request) has its own SecurityContext.

---

### **Q5: What is AuthenticationManager and how does it work?**

AuthenticationManager is the main interface responsible for authenticating users.

When user tries to login to PerformanceTrack:
```java
// User sends username and password
Authentication request = new UsernamePasswordAuthenticationToken(username, password);

// AuthenticationManager checks if credentials are valid
Authentication result = authenticationManager.authenticate(request);

// If successful, returns authenticated object with user details and roles
// If failed, throws AuthenticationException
```

Behind the scenes:
1. AuthenticationManager delegates to AuthenticationProvider
2. AuthenticationProvider uses UserDetailsService to load user from database
3. Compares provided password with stored password
4. If match, authentication succeeds

In PerformanceTrack, this happens when user calls `/api/auth/login` endpoint.

---

### **Q6: What is UserDetailsService interface?**

UserDetailsService is an interface that loads user-specific data. You implement it to tell Spring Security how to fetch user information from your database.

In PerformanceTrack:
```java
@Service
public class CustomUserDetailsService implements UserDetailsService {
    
    @Autowired
    private UserRepository userRepository;
    
    @Override
    public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
        User user = userRepository.findByUsername(username);
        
        if (user == null) {
            throw new UsernameNotFoundException("User not found: " + username);
        }
        
        return new org.springframework.security.core.userdetails.User(
            user.getUsername(),
            user.getPassword(),  // Should be encrypted
            getAuthorities(user.getRole())
        );
    }
    
    private Collection<GrantedAuthority> getAuthorities(String role) {
        return Collections.singletonList(new SimpleGrantedAuthority(role));
    }
}
```

When user logs in, Spring Security calls `loadUserByUsername()` to fetch user details from your database and validate credentials.

---

### **Q7: What is the difference between UserDetails and UserDetailsService?**

**UserDetailsService:** Interface you implement to load user from database
```java
public interface UserDetailsService {
    UserDetails loadUserByUsername(String username);
}
```

**UserDetails:** Interface representing the user information
```java
public interface UserDetails {
    String getUsername();
    String getPassword();
    Collection<GrantedAuthority> getAuthorities();
    boolean isAccountNonExpired();
    boolean isAccountNonLocked();
    boolean isEnabled();
}
```

In PerformanceTrack:
- **UserDetailsService** = The service that fetches user from database
- **UserDetails** = The actual user object with username, password, roles

Think of UserDetailsService as a librarian and UserDetails as the book. You ask the librarian (UserDetailsService) to find a book (UserDetails) by its name (username).

---

### **Q8: What is PasswordEncoder and why do we need it?**

PasswordEncoder encrypts passwords before storing them in the database. Never store plain text passwords!

In PerformanceTrack:
```java
@Configuration
public class SecurityConfig {
    
    @Bean
    public PasswordEncoder passwordEncoder() {
        return new BCryptPasswordEncoder();  // Strong encryption
    }
}
```

When user registers:
```java
@Service
public class UserService {
    
    @Autowired
    private PasswordEncoder passwordEncoder;
    
    public void registerUser(String username, String plainPassword) {
        String encryptedPassword = passwordEncoder.encode(plainPassword);
        // encryptedPassword = "$2a$10$KgP3xG..." (encrypted hash)
        
        User user = new User(username, encryptedPassword);
        userRepository.save(user);
    }
}
```

When user logs in, Spring Security automatically uses the same PasswordEncoder to compare:
```java
passwordEncoder.matches(providedPassword, storedEncryptedPassword);
```

BCryptPasswordEncoder is one-way encryption - you cannot decrypt it back to original password. This protects user passwords even if database is compromised.

---

### **Q9: What are different types of authentication in Spring Security?**

Spring Security supports multiple authentication methods:

**1. Form-based authentication (traditional web apps):**
User fills HTML form with username/password, submits to `/login`.

**2. HTTP Basic Authentication:**
```
Header: Authorization: Basic base64(username:password)
```
Simple but less secure. Used for APIs with SSL/TLS.

**3. Token-based authentication (JWT):**
User gets a token after login, sends token in subsequent requests.
```
Header: Authorization: Bearer eyJhbGciOiJIUzI1NiIs...
```
Used in PerformanceTrack REST API.

**4. OAuth2:**
Third-party login like "Login with Google" or "Login with Facebook".

**5. LDAP authentication:**
Enterprise authentication using LDAP/Active Directory.

For PerformanceTrack REST API, you use **JWT token-based authentication** because it's stateless and works well with modern frontend frameworks.

---

### **Q10: How do you configure Spring Security in Spring Boot?**

Create a configuration class extending `WebSecurityConfigurerAdapter`:

```java
@Configuration
@EnableWebSecurity
public class SecurityConfig extends WebSecurityConfigurerAdapter {
    
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http
            .csrf().disable()  // Disable CSRF for REST API
            .authorizeRequests()
                .antMatchers("/api/auth/**").permitAll()  // Allow login without authentication
                .antMatchers("/api/admin/**").hasRole("ADMIN")  // Only ADMIN can access
                .anyRequest().authenticated()  // All other requests need authentication
            .and()
            .sessionManagement()
                .sessionCreationPolicy(SessionCreationPolicy.STATELESS);  // No sessions for REST API
    }
    
    @Override
    protected void configure(AuthenticationManagerBuilder auth) throws Exception {
        auth.userDetailsService(userDetailsService)
            .passwordEncoder(passwordEncoder());
    }
    
    @Bean
    public PasswordEncoder passwordEncoder() {
        return new BCryptPasswordEncoder();
    }
}
```

This configuration tells Spring Security:
- Don't require authentication for `/api/auth/login` and `/api/auth/register`
- Only ADMIN role can access `/api/admin/**` endpoints
- All other endpoints require authentication
- Use stateless sessions (no session cookies)

---

### **Q11: What is @EnableWebSecurity annotation?**

`@EnableWebSecurity` enables Spring Security's web security support and provides Spring MVC integration.

```java
@Configuration
@EnableWebSecurity  // This enables Spring Security
public class SecurityConfig extends WebSecurityConfigurerAdapter {
    // Security configuration
}
```

When you add this annotation:
- Spring Security filter chain is activated
- Security filters are registered automatically
- Your security configuration is applied to all HTTP requests

In PerformanceTrack, without `@EnableWebSecurity`, all endpoints would be open and unsecured. With it, Spring Security protects your APIs based on your configuration.

---

### **Q12: What is the difference between hasRole() and hasAuthority()?**

Both check user permissions, but with different prefixes:

**hasRole():** Automatically adds "ROLE_" prefix
```java
.antMatchers("/api/admin/**").hasRole("ADMIN")
// Internally checks for authority "ROLE_ADMIN"
```

**hasAuthority():** Uses exact string you provide
```java
.antMatchers("/api/admin/**").hasAuthority("ROLE_ADMIN")
// Checks for authority "ROLE_ADMIN"
```

In PerformanceTrack database, you might store:
```
User: john, Role: ROLE_ADMIN
User: jane, Role: ROLE_EMPLOYEE
```

Configuration:
```java
.antMatchers("/api/admin/**").hasRole("ADMIN")  // Looks for ROLE_ADMIN
// OR
.antMatchers("/api/admin/**").hasAuthority("ROLE_ADMIN")  // Looks for ROLE_ADMIN
```

Both work the same if you store roles with "ROLE_" prefix. Use `hasRole()` for simplicity - it's cleaner to write `hasRole("ADMIN")` than `hasAuthority("ROLE_ADMIN")`.

---

### **Q13: How do you implement role-based access control?**

Role-based access control (RBAC) restricts access based on user roles.

In PerformanceTrack:

**Step 1: Define roles in database**
```
ROLE_EMPLOYEE - Can view own goals and reviews
ROLE_MANAGER - Can approve reviews for team members
ROLE_ADMIN - Can access everything
```

**Step 2: Assign roles to users**
```java
@Entity
public class User {
    private String username;
    private String password;
    private String role;  // ROLE_EMPLOYEE, ROLE_MANAGER, ROLE_ADMIN
}
```

**Step 3: Configure security**
```java
@Override
protected void configure(HttpSecurity http) throws Exception {
    http.authorizeRequests()
        .antMatchers("/api/reports/**").hasAnyRole("EMPLOYEE", "MANAGER", "ADMIN")
        .antMatchers("/api/reviews/approve/**").hasAnyRole("MANAGER", "ADMIN")
        .antMatchers("/api/admin/**").hasRole("ADMIN")
        .anyRequest().authenticated();
}
```

**Step 4: Use @PreAuthorize for method-level security**
```java
@PreAuthorize("hasRole('MANAGER')")
@PutMapping("/reviews/{id}/approve")
public Review approveReview(@PathVariable Long id) {
    return reviewService.approveReview(id);
}
```

Now only managers and admins can approve reviews. Employees trying to access this endpoint get 403 Forbidden.

---

### **Q14: What is @PreAuthorize and @PostAuthorize?**

These annotations provide method-level security using Spring Expression Language (SpEL).

**@PreAuthorize:** Checks permission BEFORE method executes
```java
@PreAuthorize("hasRole('ADMIN')")
@DeleteMapping("/reports/{id}")
public void deleteReport(@PathVariable Long id) {
    reportService.deleteReport(id);
}
```
Method executes only if user has ADMIN role.

**@PostAuthorize:** Checks permission AFTER method executes (rarely used)
```java
@PostAuthorize("returnObject.employeeId == authentication.principal.id")
@GetMapping("/reports/{id}")
public Report getReport(@PathVariable Long id) {
    return reportService.getReport(id);
}
```
Method executes, but result is returned only if the report belongs to the logged-in user.

**@Secured:** Similar to @PreAuthorize but simpler (no SpEL)
```java
@Secured("ROLE_ADMIN")
@DeleteMapping("/reports/{id}")
public void deleteReport(@PathVariable Long id) {
    reportService.deleteReport(id);
}
```

In PerformanceTrack, use `@PreAuthorize` for method-level security. Enable it with:
```java
@EnableGlobalMethodSecurity(prePostEnabled = true)
```

---

### **Q15: What is CSRF and how does Spring Security handle it?**

CSRF (Cross-Site Request Forgery) is an attack where malicious website tricks user's browser into making unwanted requests to your application.

**Example attack:**
1. User logs into PerformanceTrack (gets session cookie)
2. User visits malicious website (while still logged in)
3. Malicious website contains: `<form action="https://performancetrack.com/api/reports/delete/123" method="POST">`
4. Browser automatically sends session cookie with request
5. Your server thinks it's a legitimate request from logged-in user
6. Report gets deleted without user's knowledge!

**Spring Security's protection:**

For traditional web apps with sessions, Spring Security generates CSRF tokens:
```html
<form action="/delete" method="POST">
    <input type="hidden" name="_csrf" value="4a3b2c1d..." />
    <!-- Server validates this token -->
</form>
```

For REST APIs (like PerformanceTrack), disable CSRF because:
- No browser sessions (stateless JWT tokens)
- Tokens are not automatically sent by browser
- API clients explicitly add tokens to headers

```java
http.csrf().disable();  // Safe for REST APIs using JWT
```

---

### **Q16: What is CORS and how to configure it in Spring Security?**

CORS (Cross-Origin Resource Sharing) is a security feature that restricts which domains can access your API.

Problem in PerformanceTrack:
- Backend runs on `http://localhost:8080`
- Frontend runs on `http://localhost:3000`
- Browser blocks frontend from calling backend (different origins)

Solution - Configure CORS:
```java
@Configuration
@EnableWebSecurity
public class SecurityConfig extends WebSecurityConfigurerAdapter {
    
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http
            .cors()  // Enable CORS
            .and()
            .csrf().disable()
            .authorizeRequests()
                .anyRequest().authenticated();
    }
    
    @Bean
    public CorsConfigurationSource corsConfigurationSource() {
        CorsConfiguration configuration = new CorsConfiguration();
        configuration.setAllowedOrigins(Arrays.asList("http://localhost:3000"));
        configuration.setAllowedMethods(Arrays.asList("GET", "POST", "PUT", "DELETE"));
        configuration.setAllowedHeaders(Arrays.asList("*"));
        configuration.setAllowCredentials(true);
        
        UrlBasedCorsConfigurationSource source = new UrlBasedCorsConfigurationSource();
        source.registerCorsConfiguration("/**", configuration);
        return source;
    }
}
```

Now frontend at localhost:3000 can call your API. In production, replace with actual frontend domain.

---

### **Q17: What is the difference between STATEFUL and STATELESS authentication?**

**STATEFUL (Session-based):**
Server stores session information in memory or database.

```
User logs in → Server creates session → Sends session ID in cookie
Next request → Browser sends cookie → Server looks up session → Validates user
```

In PerformanceTrack with sessions:
- Server memory stores: `{sessionId: "abc123", user: "john", role: "ADMIN"}`
- Doesn't scale well (what if you have 1000 servers? Which has the session?)
- Requires session management

**STATELESS (Token-based, JWT):**
Server doesn't store anything. Token contains all information.

```
User logs in → Server generates JWT token → Sends token to client
Next request → Client sends token in header → Server validates token signature → Extracts user info
```

In PerformanceTrack with JWT:
- Token contains: `{user: "john", role: "ADMIN", expiry: "..."}`
- No server memory needed
- Scales horizontally (any server can validate token)
- Perfect for REST APIs

For PerformanceTrack, use STATELESS JWT authentication:
```java
.sessionManagement()
    .sessionCreationPolicy(SessionCreationPolicy.STATELESS)
```

---

### **Q18: How do you implement logout in Spring Security?**

For **session-based authentication:**
```java
http
    .logout()
        .logoutUrl("/logout")
        .logoutSuccessUrl("/login")
        .invalidateHttpSession(true)
        .deleteCookies("JSESSIONID");
```

For **JWT token-based authentication (PerformanceTrack):**

Logout is simpler - just delete token on client side!

```javascript
// Frontend code
localStorage.removeItem('jwtToken');
// User is now logged out
```

Server doesn't need to do anything because it doesn't store sessions.

**Optional: Token blacklist for JWT:**
If you want to invalidate tokens server-side (like for "logout from all devices"):

```java
@Service
public class TokenBlacklistService {
    
    private Set<String> blacklistedTokens = new HashSet<>();
    
    public void blacklistToken(String token) {
        blacklistedTokens.add(token);
    }
    
    public boolean isBlacklisted(String token) {
        return blacklistedTokens.contains(token);
    }
}
```

Check blacklist in your JWT filter before validating token. But for most applications, client-side deletion is sufficient.

---

### **Q19: What is method-level security in Spring Security?**

Method-level security applies security at the method level instead of URL level.

In PerformanceTrack:

**URL-level (in SecurityConfig):**
```java
.antMatchers("/api/admin/**").hasRole("ADMIN")
```
Protects all endpoints under `/api/admin/`.

**Method-level (on specific methods):**
```java
@Service
public class ReportService {
    
    @PreAuthorize("hasRole('ADMIN') or #employeeId == authentication.principal.id")
    public Report getEmployeeReport(Long employeeId) {
        return reportRepository.findByEmployeeId(employeeId);
    }
    
    @PreAuthorize("hasRole('MANAGER')")
    public void approveReport(Long reportId) {
        // Only managers can approve
    }
}
```

Enable method-level security:
```java
@Configuration
@EnableGlobalMethodSecurity(prePostEnabled = true)
public class SecurityConfig {
    // ...
}
```

Method-level security is more flexible - you can write complex conditions using SpEL. Use it when URL-level security is not enough.

---

### **Q20: What is SecurityFilterChain in Spring Security?**

SecurityFilterChain is a chain of security filters that process every HTTP request.

When request comes to PerformanceTrack:
```
HTTP Request
    ↓
SecurityContextPersistenceFilter
    ↓
LogoutFilter
    ↓
UsernamePasswordAuthenticationFilter (or JWT filter)
    ↓
BasicAuthenticationFilter
    ↓
FilterSecurityInterceptor (authorization check)
    ↓
Your Controller
```

You can add custom filters to this chain:
```java
http.addFilterBefore(jwtAuthenticationFilter, UsernamePasswordAuthenticationFilter.class);
```

This adds your JWT filter before the standard username/password filter in the chain.

Each filter can:
- Process the request
- Pass to next filter
- Or reject the request (return 401/403)

You don't usually interact with individual filters - Spring Security configures them based on your `SecurityConfig`.

---

**Should I continue with JWT questions? I have comprehensive coverage of:**
- JWT basics and structure
- JWT implementation in Spring Boot
- Token generation and validation
- Refresh tokens
- JWT best practices
- And then REST API advanced topics

Let me know!

## **JWT (JSON Web Token) Interview Questions**

### **Q21: What is JWT (JSON Web Token)?**

JWT is a compact, self-contained token format for securely transmitting information between parties as a JSON object.

In PerformanceTrack, when a user logs in:
```
User sends: username=john, password=pass123
Server validates and creates JWT: eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VyIjoiam9obiIsInJvbGUiOiJBRE1JTiJ9.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c
Server sends token to client
```

Client stores this token and sends it with every request:
```
GET /api/reports
Header: Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
```

Server verifies the token and extracts user information without checking database. This makes it **stateless** - server doesn't store sessions.

Think of JWT like a movie ticket. The ticket has all information (movie name, seat number, showtime). Theater staff just validates the ticket without checking any database.

---

### **Q22: What is the structure of JWT?**

JWT has three parts separated by dots (`.`):

**Header.Payload.Signature**

**1. Header** (red part):
```json
{
  "alg": "HS256",
  "typ": "JWT"
}
```
Contains algorithm used for signing (HS256 = HMAC with SHA-256).

**2. Payload** (purple part):
```json
{
  "sub": "john",
  "role": "ADMIN",
  "iat": 1701234567,
  "exp": 1701238167
}
```
Contains user information (claims). This is NOT encrypted, just Base64 encoded - anyone can decode and read it.

**3. Signature** (blue part):
```
HMACSHA256(
  base64UrlEncode(header) + "." + base64UrlEncode(payload),
  secret_key
)
```
Created by signing header + payload with secret key. This ensures token hasn't been tampered with.

In PerformanceTrack, a complete JWT looks like:
```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiJqb2huIiwicm9sZSI6IkFETUlOIn0.signature_here
```

---

### **Q23: What is the difference between signing and encryption in JWT?**

**Signing (what JWT does by default):**
Token is readable by anyone, but tamper-proof.

```
User can decode and read: {"user": "john", "role": "ADMIN"}
But cannot change role to "SUPER_ADMIN" because signature won't match
```

In PerformanceTrack:
- Anyone can decode JWT and see username and role
- But they cannot modify it without knowing the secret key
- Server validates signature to ensure token hasn't been tampered

**Encryption (optional, JWE):**
Token content is encrypted, cannot be read without decryption key.

```
Token content is gibberish until decrypted with key
```

For most applications including PerformanceTrack, **signing is sufficient**. Don't put sensitive data like passwords or credit card numbers in JWT payload. Put only identifiers (user ID, role, expiry time).

---

### **Q24: What are JWT claims?**

Claims are statements about the user stored in JWT payload. There are three types:

**1. Registered claims (standard, recommended):**
- `iss` (issuer): Who created the token
- `sub` (subject): User identifier (username or user ID)
- `aud` (audience): Who should accept the token
- `exp` (expiration): When token expires (Unix timestamp)
- `iat` (issued at): When token was created
- `nbf` (not before): Token not valid before this time

**2. Public claims:** Custom claims registered in IANA registry (rarely used)

**3. Private claims:** Custom claims you define

In PerformanceTrack JWT:
```json
{
  "sub": "john",              // Registered: subject
  "role": "ADMIN",           // Private: custom claim
  "employeeId": 123,         // Private: custom claim
  "iat": 1701234567,         // Registered: issued at
  "exp": 1701238167          // Registered: expiration
}
```

Keep payload small - it's sent with every request. Don't include unnecessary data.

---

### **Q25: How do you implement JWT authentication in Spring Boot?**

**Step 1: Add dependency**
```xml
<dependency>
    <groupId>io.jsonwebtoken</groupId>
    <artifactId>jjwt</artifactId>
    <version>0.9.1</version>
</dependency>
```

**Step 2: Create JWT utility class**
```java
@Component
public class JwtUtil {
    
    private String SECRET_KEY = "mySecretKey123";  // Store in properties file
    
    // Generate token
    public String generateToken(String username, String role) {
        Map<String, Object> claims = new HashMap<>();
        claims.put("role", role);
        
        return Jwts.builder()
                .setClaims(claims)
                .setSubject(username)
                .setIssuedAt(new Date())
                .setExpiration(new Date(System.currentTimeMillis() + 1000 * 60 * 60))  // 1 hour
                .signWith(SignatureAlgorithm.HS256, SECRET_KEY)
                .compact();
    }
    
    // Validate token
    public boolean validateToken(String token) {
        try {
            Jwts.parser().setSigningKey(SECRET_KEY).parseClaimsJws(token);
            return true;
        } catch (Exception e) {
            return false;
        }
    }
    
    // Extract username from token
    public String extractUsername(String token) {
        return Jwts.parser()
                .setSigningKey(SECRET_KEY)
                .parseClaimsJws(token)
                .getBody()
                .getSubject();
    }
    
    // Check if token expired
    public boolean isTokenExpired(String token) {
        Date expiration = Jwts.parser()
                .setSigningKey(SECRET_KEY)
                .parseClaimsJws(token)
                .getBody()
                .getExpiration();
        return expiration.before(new Date());
    }
}
```

**Step 3: Create login endpoint**
```java
@RestController
@RequestMapping("/api/auth")
public class AuthController {
    
    @Autowired
    private AuthenticationManager authenticationManager;
    
    @Autowired
    private JwtUtil jwtUtil;
    
    @PostMapping("/login")
    public ResponseEntity<?> login(@RequestBody LoginRequest request) {
        try {
            authenticationManager.authenticate(
                new UsernamePasswordAuthenticationToken(request.getUsername(), request.getPassword())
            );
            
            String token = jwtUtil.generateToken(request.getUsername(), "ADMIN");
            return ResponseEntity.ok(new AuthResponse(token));
            
        } catch (BadCredentialsException e) {
            return ResponseEntity.status(HttpStatus.UNAUTHORIZED).body("Invalid credentials");
        }
    }
}
```

---

### **Q26: How do you create a JWT filter in Spring Security?**

Create a filter that intercepts requests and validates JWT tokens.

```java
@Component
public class JwtAuthenticationFilter extends OncePerRequestFilter {
    
    @Autowired
    private JwtUtil jwtUtil;
    
    @Autowired
    private UserDetailsService userDetailsService;
    
    @Override
    protected void doFilterInternal(HttpServletRequest request, 
                                   HttpServletResponse response, 
                                   FilterChain filterChain) throws ServletException, IOException {
        
        // Extract token from header
        String authHeader = request.getHeader("Authorization");
        String token = null;
        String username = null;
        
        if (authHeader != null && authHeader.startsWith("Bearer ")) {
            token = authHeader.substring(7);  // Remove "Bearer " prefix
            username = jwtUtil.extractUsername(token);
        }
        
        // Validate token
        if (username != null && SecurityContextHolder.getContext().getAuthentication() == null) {
            
            UserDetails userDetails = userDetailsService.loadUserByUsername(username);
            
            if (jwtUtil.validateToken(token) && !jwtUtil.isTokenExpired(token)) {
                
                UsernamePasswordAuthenticationToken authToken = 
                    new UsernamePasswordAuthenticationToken(
                        userDetails, 
                        null, 
                        userDetails.getAuthorities()
                    );
                
                SecurityContextHolder.getContext().setAuthentication(authToken);
            }
        }
        
        filterChain.doFilter(request, response);
    }
}
```

**Add filter to security configuration:**
```java
@Configuration
@EnableWebSecurity
public class SecurityConfig extends WebSecurityConfigurerAdapter {
    
    @Autowired
    private JwtAuthenticationFilter jwtAuthenticationFilter;
    
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http
            .csrf().disable()
            .authorizeRequests()
                .antMatchers("/api/auth/**").permitAll()
                .anyRequest().authenticated()
            .and()
            .sessionManagement()
                .sessionCreationPolicy(SessionCreationPolicy.STATELESS);
        
        // Add JWT filter before UsernamePasswordAuthenticationFilter
        http.addFilterBefore(jwtAuthenticationFilter, UsernamePasswordAuthenticationFilter.class);
    }
}
```

Now every request goes through JWT filter, which validates token and sets authentication in SecurityContext.

---

### **Q27: What is the difference between symmetric and asymmetric signing in JWT?**

**Symmetric (HMAC - what PerformanceTrack uses):**
Same secret key for signing and verifying.

```java
String SECRET_KEY = "mySecretKey123";

// Sign
String token = Jwts.builder()
    .setSubject("john")
    .signWith(SignatureAlgorithm.HS256, SECRET_KEY)
    .compact();

// Verify
Jwts.parser().setSigningKey(SECRET_KEY).parseClaimsJws(token);
```

Pros: Fast, simple
Cons: Same key on all servers - if leaked, attacker can create valid tokens

**Asymmetric (RSA):**
Private key for signing, public key for verifying.

```java
// Sign with private key (only auth server has this)
String token = Jwts.builder()
    .setSubject("john")
    .signWith(SignatureAlgorithm.RS256, privateKey)
    .compact();

// Verify with public key (all servers have this)
Jwts.parser().setSigningKey(publicKey).parseClaimsJws(token);
```

Pros: More secure - even if public key is leaked, attacker cannot create tokens
Cons: Slower, more complex setup

For PerformanceTrack (single application), symmetric is fine. For microservices with separate auth server, use asymmetric.

---

### **Q28: How do you handle JWT token expiration?**

Set expiration time when creating token:

```java
public String generateToken(String username) {
    return Jwts.builder()
            .setSubject(username)
            .setIssuedAt(new Date())
            .setExpiration(new Date(System.currentTimeMillis() + 1000 * 60 * 60))  // 1 hour
            .signWith(SignatureAlgorithm.HS256, SECRET_KEY)
            .compact();
}
```

Check expiration when validating:
```java
public boolean isTokenExpired(String token) {
    Date expiration = Jwts.parser()
            .setSigningKey(SECRET_KEY)
            .parseClaimsJws(token)
            .getBody()
            .getExpiration();
    return expiration.before(new Date());
}
```

When token expires, client gets 401 Unauthorized. Frontend should:
1. Catch 401 error
2. Redirect user to login page
3. Or use refresh token to get new access token

In PerformanceTrack:
- Short-lived access tokens (15 minutes - 1 hour) for security
- Long-lived refresh tokens (1 day - 1 week) for convenience

---

### **Q29: What is the difference between access token and refresh token?**

**Access Token:**
- Short-lived (15 minutes - 1 hour)
- Sent with every API request
- Contains user permissions
- If stolen, damage is limited because it expires quickly

**Refresh Token:**
- Long-lived (1 day - 1 week)
- Used only to get new access token
- Stored securely (HTTP-only cookie or secure storage)
- If stolen, can be revoked in database

In PerformanceTrack flow:
```
1. User logs in
   → Server returns: accessToken (expires in 15 min) + refreshToken (expires in 7 days)

2. User makes API calls
   → Sends accessToken in header

3. After 15 minutes, accessToken expires
   → API returns 401 Unauthorized

4. Frontend sends refreshToken to /api/auth/refresh
   → Server validates refreshToken from database
   → Returns new accessToken (expires in 15 min)

5. User continues using app with new accessToken

6. After 7 days, refreshToken expires
   → User must login again
```

Implementation:
```java
@PostMapping("/refresh")
public ResponseEntity<?> refreshToken(@RequestBody RefreshTokenRequest request) {
    String refreshToken = request.getRefreshToken();
    
    // Validate refresh token from database
    if (refreshTokenService.isValid(refreshToken)) {
        String username = refreshTokenService.getUsername(refreshToken);
        String newAccessToken = jwtUtil.generateToken(username);
        return ResponseEntity.ok(new AuthResponse(newAccessToken));
    }
    
    return ResponseEntity.status(HttpStatus.UNAUTHORIZED).body("Invalid refresh token");
}
```

---

### **Q30: How do you store refresh tokens securely?**

Store refresh tokens in database with user association:

```java
@Entity
public class RefreshToken {
    @Id
    @GeneratedValue
    private Long id;
    
    private String token;
    
    @ManyToOne
    private User user;
    
    private LocalDateTime expiryDate;
    
    private boolean revoked;  // For logout/security
}
```

**RefreshTokenService:**
```java
@Service
public class RefreshTokenService {
    
    @Autowired
    private RefreshTokenRepository refreshTokenRepository;
    
    public RefreshToken createRefreshToken(User user) {
        RefreshToken refreshToken = new RefreshToken();
        refreshToken.setToken(UUID.randomUUID().toString());
        refreshToken.setUser(user);
        refreshToken.setExpiryDate(LocalDateTime.now().plusDays(7));
        refreshToken.setRevoked(false);
        
        return refreshTokenRepository.save(refreshToken);
    }
    
    public boolean isValid(String token) {
        RefreshToken refreshToken = refreshTokenRepository.findByToken(token);
        
        if (refreshToken == null || refreshToken.isRevoked()) {
            return false;
        }
        
        if (refreshToken.getExpiryDate().isBefore(LocalDateTime.now())) {
            return false;  // Expired
        }
        
        return true;
    }
    
    public void revokeToken(String token) {
        RefreshToken refreshToken = refreshTokenRepository.findByToken(token);
        if (refreshToken != null) {
            refreshToken.setRevoked(true);
            refreshTokenRepository.save(refreshToken);
        }
    }
}
```

On logout:
```java
@PostMapping("/logout")
public ResponseEntity<?> logout(@RequestBody LogoutRequest request) {
    refreshTokenService.revokeToken(request.getRefreshToken());
    return ResponseEntity.ok("Logged out successfully");
}
```

This allows you to revoke refresh tokens when user logs out or if security breach is detected.

---

### **Q31: What are JWT best practices?**

**1. Use HTTPS always:**
Without HTTPS, tokens can be intercepted. Always use SSL/TLS in production.

**2. Keep tokens short-lived:**
Access tokens: 15-60 minutes
Refresh tokens: 1-7 days

**3. Don't store sensitive data in payload:**
```java
// BAD
{"username": "john", "password": "secret123", "creditCard": "1234..."}

// GOOD
{"username": "john", "role": "ADMIN", "userId": 123}
```

**4. Store secret key securely:**
```properties
# application.properties
jwt.secret=${JWT_SECRET_KEY}  # Use environment variable
```

**5. Validate token on every request:**
Check signature, expiration, and claims.

**6. Implement token revocation for refresh tokens:**
Store refresh tokens in database so you can revoke them.

**7. Add jti (JWT ID) claim for tracking:**
```java
.claim("jti", UUID.randomUUID().toString())
```

**8. Use appropriate signature algorithm:**
HS256 (symmetric) for single app
RS256 (asymmetric) for microservices

**9. Implement rate limiting on login endpoint:**
Prevent brute force attacks.

**10. Add token to blacklist on logout (optional):**
For access tokens, if you need instant revocation.

---

### **Q32: How do you invalidate JWT tokens?**

Problem: JWT tokens are stateless - server doesn't store them, so how to invalidate?

**Solution 1: Wait for expiration (simplest)**
Keep tokens short-lived. When user logs out, delete token from client. User can't use app anymore even though token is technically valid for a few more minutes.

**Solution 2: Token blacklist**
```java
@Service
public class TokenBlacklistService {
    
    private Set<String> blacklistedTokens = new HashSet<>();
    
    public void blacklistToken(String token) {
        blacklistedTokens.add(token);
        // In production, use Redis with expiration time
    }
    
    public boolean isBlacklisted(String token) {
        return blacklistedTokens.contains(token);
    }
}
```

In JWT filter:
```java
if (jwtUtil.validateToken(token) && !tokenBlacklistService.isBlacklisted(token)) {
    // Allow request
}
```

On logout:
```java
@PostMapping("/logout")
public ResponseEntity<?> logout(@RequestHeader("Authorization") String authHeader) {
    String token = authHeader.substring(7);
    tokenBlacklistService.blacklistToken(token);
    return ResponseEntity.ok("Logged out");
}
```

**Solution 3: Use refresh tokens with database**
Access tokens remain stateless. Only refresh tokens are stored and can be revoked.

For PerformanceTrack, use **Solution 1** (wait for expiration) with short-lived tokens for simplicity.

---

### **Q33: What is the jti claim and why is it useful?**

`jti` (JWT ID) is a unique identifier for each token.

```java
public String generateToken(String username) {
    return Jwts.builder()
            .setSubject(username)
            .claim("jti", UUID.randomUUID().toString())  // Unique token ID
            .setIssuedAt(new Date())
            .setExpiration(new Date(System.currentTimeMillis() + 1000 * 60 * 60))
            .signWith(SignatureAlgorithm.HS256, SECRET_KEY)
            .compact();
}
```

**Uses:**

**1. Track individual tokens:**
Each token has unique ID. You can log which token was used for which action.

**2. Revoke specific tokens:**
```java
@Service
public class TokenRevocationService {
    private Set<String> revokedTokenIds = new HashSet<>();
    
    public void revokeToken(String jti) {
        revokedTokenIds.add(jti);
    }
    
    public boolean isRevoked(String jti) {
        return revokedTokenIds.contains(jti);
    }
}
```

**3. Prevent token replay attacks:**
Store used token IDs. If same token is used again, you know it's being replayed.

**4. "Logout from all devices" feature:**
When user changes password, revoke all tokens issued before password change by comparing `jti` with database records.

In PerformanceTrack, `jti` is optional but useful for enterprise features like device management and security auditing.

---

### **Q34: How do you extract custom claims from JWT?**

When generating token with custom claims:
```java
public String generateToken(String username, Long employeeId, String department) {
    Map<String, Object> claims = new HashMap<>();
    claims.put("employeeId", employeeId);
    claims.put("department", department);
    claims.put("role", "MANAGER");
    
    return Jwts.builder()
            .setClaims(claims)
            .setSubject(username)
            .setIssuedAt(new Date())
            .setExpiration(new Date(System.currentTimeMillis() + 1000 * 60 * 60))
            .signWith(SignatureAlgorithm.HS256, SECRET_KEY)
            .compact();
}
```

Extract claims:
```java
public Claims extractAllClaims(String token) {
    return Jwts.parser()
            .setSigningKey(SECRET_KEY)
            .parseClaimsJws(token)
            .getBody();
}

public String extractUsername(String token) {
    return extractAllClaims(token).getSubject();
}

public Long extractEmployeeId(String token) {
    return extractAllClaims(token).get("employeeId", Long.class);
}

public String extractDepartment(String token) {
    return extractAllClaims(token).get("department", String.class);
}

public String extractRole(String token) {
    return extractAllClaims(token).get("role", String.class);
}
```

Use in controller:
```java
@GetMapping("/my-reports")
public List<Report> getMyReports(@RequestHeader("Authorization") String authHeader) {
    String token = authHeader.substring(7);
    Long employeeId = jwtUtil.extractEmployeeId(token);
    return reportService.getReportsByEmployee(employeeId);
}
```

Or get from SecurityContext (if JWT filter populated it):
```java
@GetMapping("/my-reports")
public List<Report> getMyReports() {
    Authentication auth = SecurityContextHolder.getContext().getAuthentication();
    String username = auth.getName();
    return reportService.getReportsByUsername(username);
}
```

---

### **Q35: What are common JWT vulnerabilities and how to prevent them?**

**1. None Algorithm Attack**
Attacker changes algorithm to "none" and removes signature.

Prevention:
```java
// Explicitly specify algorithm, don't allow "none"
Jwts.parser().setSigningKey(SECRET_KEY).parseClaimsJws(token);
```

**2. Weak Secret Key**
Short or predictable secret keys can be brute-forced.

Prevention:
```java
// Use long, random secret key (minimum 256 bits for HS256)
private String SECRET_KEY = "aVeryLongRandomSecretKeyWithAtLeast256Bits123456789";
```

**3. Token in URL**
```
GET /api/reports?token=eyJhbGci...
```
URL is logged in server logs, browser history, etc.

Prevention: Always use Authorization header:
```
Header: Authorization: Bearer eyJhbGci...
```

**4. XSS (Cross-Site Scripting)**
If attacker injects JavaScript, they can steal tokens from localStorage.

Prevention:
- Store tokens in HTTP-only cookies (can't be accessed by JavaScript)
- Or use secure storage with proper XSS protection
- Sanitize all user inputs

**5. Missing Expiration**
Token never expires.

Prevention: Always set expiration:
```java
.setExpiration(new Date(System.currentTimeMillis() + 1000 * 60 * 60))
```

**6. Exposed Secret Key**
Secret key in source code or public repository.

Prevention: Use environment variables:
```properties
jwt.secret=${JWT_SECRET_KEY}
```

---

### **Q36: How do you handle "remember me" functionality with JWT?**

Implement using different token expiration times:

```java
@PostMapping("/login")
public ResponseEntity<?> login(@RequestBody LoginRequest request) {
    // Authenticate user
    authenticationManager.authenticate(
        new UsernamePasswordAuthenticationToken(request.getUsername(), request.getPassword())
    );
    
    // Generate tokens based on "remember me"
    long expirationTime;
    if (request.isRememberMe()) {
        expirationTime = 1000 * 60 * 60 * 24 * 30;  // 30 days
    } else {
        expirationTime = 1000 * 60 * 60;  // 1 hour
    }
    
    String accessToken = jwtUtil.generateToken(request.getUsername(), expirationTime);
    return ResponseEntity.ok(new AuthResponse(accessToken));
}
```

Better approach with refresh tokens:
```java
@PostMapping("/login")
public ResponseEntity<?> login(@RequestBody LoginRequest request) {
    // Authenticate
    authenticationManager.authenticate(
        new UsernamePasswordAuthenticationToken(request.getUsername(), request.getPassword())
    );
    
    User user = userRepository.findByUsername(request.getUsername());
    
    // Always short-lived access token
    String accessToken = jwtUtil.generateAccessToken(user.getUsername());  // 15 min
    
    // Refresh token expiration based on "remember me"
    long refreshTokenExpiry;
    if (request.isRememberMe()) {
        refreshTokenExpiry = 30;  // 30 days
    } else {
        refreshTokenExpiry = 1;   // 1 day
    }
    
    RefreshToken refreshToken = refreshTokenService.createRefreshToken(user, refreshTokenExpiry);
    
    return ResponseEntity.ok(new AuthResponse(accessToken, refreshToken.getToken()));
}
```

This way, "remember me" only affects refresh token lifetime, keeping access tokens short for security.

---

### **Q37: How do you implement JWT with roles and permissions?**

Store roles/permissions in JWT claims:

```java
public String generateToken(User user) {
    Map<String, Object> claims = new HashMap<>();
    
    // Add role
    claims.put("role", user.getRole());  // "ADMIN", "MANAGER", "EMPLOYEE"
    
    // Add permissions (optional, if you have fine-grained permissions)
    List<String> permissions = user.getPermissions();  // ["READ_REPORTS", "APPROVE_REVIEWS"]
    claims.put("permissions", permissions);
    
    return Jwts.builder()
            .setClaims(claims)
            .setSubject(user.getUsername())
            .setIssuedAt(new Date())
            .setExpiration(new Date(System.currentTimeMillis() + 1000 * 60 * 60))
            .signWith(SignatureAlgorithm.HS256, SECRET_KEY)
            .compact();
}
```

In JWT filter, set authorities in SecurityContext:
```java
@Override
protected void doFilterInternal(HttpServletRequest request, HttpServletResponse response, FilterChain filterChain) {
    String token = extractTokenFromHeader(request);
    
    if (token != null && jwtUtil.validateToken(token)) {
        Claims claims = jwtUtil.extractAllClaims(token);
        String username = claims.getSubject();
        String role = claims.get("role", String.class);
        
        // Create authorities
        List<GrantedAuthority> authorities = new ArrayList<>();
        authorities.add(new SimpleGrantedAuthority("ROLE_" + role));
        
        // Add permissions if present
        List<String> permissions = claims.get("permissions", List.class);
        if (permissions != null) {
            for (String permission : permissions) {
                authorities.add(new SimpleGrantedAuthority(permission));
            }
        }
        
        UsernamePasswordAuthenticationToken authToken = 
            new UsernamePasswordAuthenticationToken(username, null, authorities);
        
        SecurityContextHolder.getContext().setAuthentication(authToken);
    }
    
    filterChain.doFilter(request, response);
}
```

Now you can use role-based and permission-based security:
```java
@PreAuthorize("hasRole('ADMIN')")
@DeleteMapping("/reports/{id}")
public void deleteReport(@PathVariable Long id) {
    reportService.deleteReport(id);
}

@PreAuthorize("hasAuthority('APPROVE_REVIEWS')")
@PutMapping("/reviews/{id}/approve")
public Review approveReview(@PathVariable Long id) {
    return reviewService.approveReview(id);
}
```

---

**Should I continue with REST API advanced questions? I have comprehensive coverage of:**
- REST API design principles
- HTTP status codes usage
- API versioning
- Error handling
- API documentation
- Rate limiting
- Caching
- File uploads/downloads
- Pagination and filtering
- HATEOAS
- And more...

Let me know!

## **REST API Advanced Interview Questions**

### **Q38: What are REST API design best practices?**

**1. Use nouns, not verbs in URLs:**
```
Good: GET /api/employees, POST /api/goals
Bad: GET /api/getEmployees, POST /api/createGoal
```

**2. Use plural nouns for collections:**
```
Good: GET /api/employees, GET /api/reports
Bad: GET /api/employee, GET /api/report
```

**3. Use proper HTTP methods:**
- GET - Retrieve data
- POST - Create new resource
- PUT - Update entire resource
- PATCH - Update partial resource
- DELETE - Remove resource

**4. Use hierarchical URLs for relationships:**
```
GET /api/employees/123/goals        - Get goals of employee 123
GET /api/employees/123/reviews/456  - Get review 456 of employee 123
```

**5. Use query parameters for filtering, sorting, pagination:**
```
GET /api/reports?year=2024&department=IT&page=0&size=10&sort=date,desc
```

**6. Version your API:**
```
/api/v1/reports
/api/v2/reports
```

**7. Return appropriate status codes:**
```
200 - Success
201 - Created
204 - No Content (successful deletion)
400 - Bad Request
401 - Unauthorized
403 - Forbidden
404 - Not Found
500 - Internal Server Error
```

**8. Use consistent response format:**
```json
{
  "status": "success",
  "data": { ... },
  "message": "Report created successfully"
}
```

In PerformanceTrack, following these practices makes API predictable and easy to use.

---

### **Q39: How do you design RESTful URLs?**

Follow a consistent pattern based on resources and their relationships.

**In PerformanceTrack:**

**Employees (top-level resource):**
```
GET    /api/employees           - List all employees
POST   /api/employees           - Create new employee
GET    /api/employees/123       - Get employee 123
PUT    /api/employees/123       - Update employee 123
DELETE /api/employees/123       - Delete employee 123
```

**Goals (sub-resource of employees):**
```
GET    /api/employees/123/goals           - Get all goals of employee 123
POST   /api/employees/123/goals           - Create goal for employee 123
GET    /api/employees/123/goals/456       - Get goal 456 of employee 123
PUT    /api/employees/123/goals/456       - Update goal 456
DELETE /api/employees/123/goals/456       - Delete goal 456
```

**Reviews:**
```
GET    /api/reviews                       - Get all reviews (with filters)
GET    /api/reviews?status=pending        - Get pending reviews
GET    /api/employees/123/reviews         - Get reviews for employee 123
POST   /api/reviews                       - Create new review
PUT    /api/reviews/789/approve           - Approve review 789
```

**Reports (analytics, not CRUD):**
```
GET    /api/reports/performance           - Performance report
GET    /api/reports/performance?year=2024&department=IT
GET    /api/reports/analytics             - Analytics dashboard
```

**Key principles:**
- Resources are nouns, not actions
- Use hierarchy for relationships
- Keep URLs readable and predictable
- Actions that don't fit CRUD can use verbs at the end (like `/approve`, `/submit`)

---

### **Q40: What is the difference between PUT and PATCH?**

**PUT:** Replace entire resource
```java
// Current resource in database
{
  "id": 1,
  "title": "Improve Code Quality",
  "description": "Reduce bugs by 20%",
  "deadline": "2024-12-31"
}

// PUT request with partial data
PUT /api/goals/1
{
  "title": "New Title"
}

// Result - ALL fields updated, missing fields become null
{
  "id": 1,
  "title": "New Title",
  "description": null,     // Lost!
  "deadline": null         // Lost!
}
```

**PATCH:** Update only specified fields
```java
// Current resource in database
{
  "id": 1,
  "title": "Improve Code Quality",
  "description": "Reduce bugs by 20%",
  "deadline": "2024-12-31"
}

// PATCH request with partial data
PATCH /api/goals/1
{
  "title": "New Title"
}

// Result - Only title updated, other fields remain
{
  "id": 1,
  "title": "New Title",
  "description": "Reduce bugs by 20%",  // Preserved
  "deadline": "2024-12-31"               // Preserved
}
```

**Implementation in PerformanceTrack:**

**PUT endpoint:**
```java
@PutMapping("/goals/{id}")
public Goal updateGoal(@PathVariable Long id, @RequestBody Goal goal) {
    goal.setId(id);
    return goalService.replaceGoal(goal);  // Replaces entire goal
}
```

**PATCH endpoint:**
```java
@PatchMapping("/goals/{id}")
public Goal partialUpdateGoal(@PathVariable Long id, @RequestBody Map<String, Object> updates) {
    return goalService.partialUpdate(id, updates);  // Updates only provided fields
}

// Service method
public Goal partialUpdate(Long id, Map<String, Object> updates) {
    Goal goal = goalRepository.findById(id);
    
    if (updates.containsKey("title")) {
        goal.setTitle((String) updates.get("title"));
    }
    if (updates.containsKey("deadline")) {
        goal.setDeadline((LocalDate) updates.get("deadline"));
    }
    
    return goalRepository.save(goal);
}
```

Use **PUT** when client sends complete resource. Use **PATCH** for partial updates (more common in real APIs).

---

### **Q41: How do you handle API errors properly?**

Create a consistent error response structure across all endpoints.

**Step 1: Create error response class**
```java
public class ErrorResponse {
    private String error;
    private String message;
    private int status;
    private LocalDateTime timestamp;
    private String path;
    
    // Constructor, getters, setters
}
```

**Step 2: Create custom exceptions**
```java
public class ResourceNotFoundException extends RuntimeException {
    public ResourceNotFoundException(String message) {
        super(message);
    }
}

public class ValidationException extends RuntimeException {
    private Map<String, String> errors;
    
    public ValidationException(String message, Map<String, String> errors) {
        super(message);
        this.errors = errors;
    }
}
```

**Step 3: Global exception handler**
```java
@RestControllerAdvice
public class GlobalExceptionHandler {
    
    @ExceptionHandler(ResourceNotFoundException.class)
    public ResponseEntity<ErrorResponse> handleNotFound(
            ResourceNotFoundException ex, 
            HttpServletRequest request) {
        
        ErrorResponse error = new ErrorResponse(
            "RESOURCE_NOT_FOUND",
            ex.getMessage(),
            HttpStatus.NOT_FOUND.value(),
            LocalDateTime.now(),
            request.getRequestURI()
        );
        
        return ResponseEntity.status(HttpStatus.NOT_FOUND).body(error);
    }
    
    @ExceptionHandler(ValidationException.class)
    public ResponseEntity<ErrorResponse> handleValidation(
            ValidationException ex, 
            HttpServletRequest request) {
        
        ErrorResponse error = new ErrorResponse(
            "VALIDATION_ERROR",
            ex.getMessage(),
            HttpStatus.BAD_REQUEST.value(),
            LocalDateTime.now(),
            request.getRequestURI()
        );
        error.setValidationErrors(ex.getErrors());
        
        return ResponseEntity.status(HttpStatus.BAD_REQUEST).body(error);
    }
    
    @ExceptionHandler(Exception.class)
    public ResponseEntity<ErrorResponse> handleGeneral(
            Exception ex, 
            HttpServletRequest request) {
        
        ErrorResponse error = new ErrorResponse(
            "INTERNAL_ERROR",
            "An unexpected error occurred",
            HttpStatus.INTERNAL_SERVER_ERROR.value(),
            LocalDateTime.now(),
            request.getRequestURI()
        );
        
        return ResponseEntity.status(HttpStatus.INTERNAL_SERVER_ERROR).body(error);
    }
}
```

**Step 4: Use in controllers**
```java
@GetMapping("/reports/{id}")
public Report getReport(@PathVariable Long id) {
    return reportService.getReport(id)
        .orElseThrow(() -> new ResourceNotFoundException("Report not found with id: " + id));
}
```

**Client receives consistent errors:**
```json
{
  "error": "RESOURCE_NOT_FOUND",
  "message": "Report not found with id: 123",
  "status": 404,
  "timestamp": "2024-12-10T10:30:00",
  "path": "/api/reports/123"
}
```

---

### **Q42: What HTTP status codes should you use in REST APIs?**

**2xx - Success:**

**200 OK** - Successful GET, PUT, PATCH
```java
@GetMapping("/reports/{id}")
public ResponseEntity<Report> getReport(@PathVariable Long id) {
    return ResponseEntity.ok(report);  // 200
}
```

**201 Created** - Successful POST (resource created)
```java
@PostMapping("/goals")
public ResponseEntity<Goal> createGoal(@RequestBody Goal goal) {
    Goal saved = goalService.save(goal);
    return ResponseEntity.status(HttpStatus.CREATED).body(saved);  // 201
}
```

**204 No Content** - Successful DELETE
```java
@DeleteMapping("/reports/{id}")
public ResponseEntity<Void> deleteReport(@PathVariable Long id) {
    reportService.delete(id);
    return ResponseEntity.noContent().build();  // 204
}
```

**4xx - Client Errors:**

**400 Bad Request** - Invalid input data
```java
// When validation fails
{
  "error": "VALIDATION_ERROR",
  "message": "Title is required"
}
```

**401 Unauthorized** - Not authenticated (no token or invalid token)
```java
// When JWT token is missing or expired
{
  "error": "UNAUTHORIZED",
  "message": "Authentication required"
}
```

**403 Forbidden** - Authenticated but not authorized
```java
// When employee tries to access admin endpoint
{
  "error": "FORBIDDEN",
  "message": "Access denied. Admin role required"
}
```

**404 Not Found** - Resource doesn't exist
```java
// When report with ID doesn't exist
{
  "error": "RESOURCE_NOT_FOUND",
  "message": "Report not found with id: 123"
}
```

**409 Conflict** - Resource already exists or constraint violation
```java
// When trying to create duplicate username
{
  "error": "CONFLICT",
  "message": "Username already exists"
}
```

**5xx - Server Errors:**

**500 Internal Server Error** - Unexpected server error
```java
// When database connection fails or unexpected exception
{
  "error": "INTERNAL_ERROR",
  "message": "An unexpected error occurred"
}
```

**503 Service Unavailable** - Server temporarily unavailable
```java
// During maintenance or when service is down
{
  "error": "SERVICE_UNAVAILABLE",
  "message": "Service is temporarily unavailable"
}
```

In PerformanceTrack, use these codes consistently to communicate what happened with the request.

---

### **Q43: How do you implement API versioning?**

**Method 1: URI Versioning (Most common and recommended)**
```java
@RestController
@RequestMapping("/api/v1/reports")
public class ReportControllerV1 {
    @GetMapping
    public List<Report> getReports() {
        // Version 1 implementation
    }
}

@RestController
@RequestMapping("/api/v2/reports")
public class ReportControllerV2 {
    @GetMapping
    public List<ReportDTO> getReports() {
        // Version 2 with different response format
    }
}
```

Pros: Clear, visible in URL, easy to route
Cons: URL changes with version

**Method 2: Request Parameter**
```java
@GetMapping("/api/reports")
public ResponseEntity<?> getReports(@RequestParam(defaultValue = "1") int version) {
    if (version == 1) {
        return ResponseEntity.ok(reportServiceV1.getReports());
    } else if (version == 2) {
        return ResponseEntity.ok(reportServiceV2.getReports());
    }
}
```
Usage: `GET /api/reports?version=2`

**Method 3: Header Versioning**
```java
@GetMapping(value = "/api/reports", headers = "API-Version=1")
public List<Report> getReportsV1() {
    // Version 1
}

@GetMapping(value = "/api/reports", headers = "API-Version=2")
public List<ReportDTO> getReportsV2() {
    // Version 2
}
```
Usage: `GET /api/reports` with header `API-Version: 2`

**Method 4: Content Negotiation**
```java
@GetMapping(value = "/api/reports", produces = "application/vnd.company.v1+json")
public List<Report> getReportsV1() {
    // Version 1
}

@GetMapping(value = "/api/reports", produces = "application/vnd.company.v2+json")
public List<ReportDTO> getReportsV2() {
    // Version 2
}
```
Usage: `GET /api/reports` with header `Accept: application/vnd.company.v2+json`

**For PerformanceTrack, use URI versioning (`/api/v1/`, `/api/v2/`)** - it's simplest and most visible.

---

### **Q44: How do you document REST APIs?**

**Method 1: Swagger/OpenAPI (Most popular)**

Add dependency:
```xml
<dependency>
    <groupId>io.springdoc</groupId>
    <artifactId>springdoc-openapi-ui</artifactId>
    <version>1.6.15</version>
</dependency>
```

Add annotations to controllers:
```java
@RestController
@RequestMapping("/api/v1/reports")
@Tag(name = "Reports", description = "Performance Reports API")
public class ReportController {
    
    @Operation(summary = "Get all reports", description = "Returns list of all performance reports")
    @ApiResponses(value = {
        @ApiResponse(responseCode = "200", description = "Successfully retrieved reports"),
        @ApiResponse(responseCode = "401", description = "Unauthorized - JWT token required")
    })
    @GetMapping
    public List<Report> getAllReports(
            @Parameter(description = "Filter by year") @RequestParam(required = false) Integer year,
            @Parameter(description = "Filter by department") @RequestParam(required = false) String department) {
        return reportService.getAllReports(year, department);
    }
    
    @Operation(summary = "Get report by ID")
    @GetMapping("/{id}")
    public ResponseEntity<Report> getReport(
            @Parameter(description = "Report ID") @PathVariable Long id) {
        return ResponseEntity.ok(reportService.getReport(id));
    }
    
    @Operation(summary = "Create new report")
    @PostMapping
    public ResponseEntity<Report> createReport(@RequestBody Report report) {
        return ResponseEntity.status(HttpStatus.CREATED)
                           .body(reportService.createReport(report));
    }
}
```

Access documentation:
```
http://localhost:8080/swagger-ui.html
```

You get interactive documentation where you can test APIs directly from browser.

**Method 2: Spring REST Docs (Test-driven)**
Generates documentation from tests. More accurate but requires more effort.

**Method 3: Manual documentation**
Create README with API endpoints, examples, and responses.

For PerformanceTrack, **use Swagger** - it's automatic, interactive, and widely used in industry.

---

### **Q45: How do you implement pagination in REST API?**

Use `Pageable` parameter and return `Page` object:

```java
@GetMapping("/reports")
public ResponseEntity<Page<Report>> getReports(
        @RequestParam(required = false) String department,
        Pageable pageable) {
    
    Page<Report> reports = reportService.getReports(department, pageable);
    return ResponseEntity.ok(reports);
}
```

Client calls:
```
GET /api/reports?page=0&size=10&sort=createdDate,desc
```

Response:
```json
{
  "content": [
    {"id": 1, "title": "Q1 Report"},
    {"id": 2, "title": "Q2 Report"}
  ],
  "pageable": {
    "pageNumber": 0,
    "pageSize": 10,
    "sort": {"sorted": true, "unsorted": false}
  },
  "totalElements": 45,
  "totalPages": 5,
  "last": false,
  "first": true,
  "number": 0,
  "size": 10
}
```

**Custom pagination response:**
```java
public class PageResponse<T> {
    private List<T> data;
    private int currentPage;
    private int totalPages;
    private long totalElements;
    private int pageSize;
    
    public static <T> PageResponse<T> of(Page<T> page) {
        PageResponse<T> response = new PageResponse<>();
        response.setData(page.getContent());
        response.setCurrentPage(page.getNumber());
        response.setTotalPages(page.getTotalPages());
        response.setTotalElements(page.getTotalElements());
        response.setPageSize(page.getSize());
        return response;
    }
}

@GetMapping("/reports")
public ResponseEntity<PageResponse<Report>> getReports(Pageable pageable) {
    Page<Report> page = reportService.getReports(pageable);
    return ResponseEntity.ok(PageResponse.of(page));
}
```

**Disable pagination for specific endpoint:**
```java
@GetMapping("/all-reports")
public List<Report> getAllReports() {
    return reportService.getAllReports();  // No pagination
}
```

---

### **Q46: How do you implement filtering and searching in REST API?**

**Simple filtering with @RequestParam:**
```java
@GetMapping("/reports")
public List<Report> getReports(
        @RequestParam(required = false) Integer year,
        @RequestParam(required = false) String department,
        @RequestParam(required = false) String status) {
    
    return reportService.getReports(year, department, status);
}
```

Usage: `GET /api/reports?year=2024&department=IT&status=completed`

**Using Specification for dynamic queries:**
```java
public class ReportSpecification {
    
    public static Specification<Report> hasYear(Integer year) {
        return (root, query, builder) -> 
            year == null ? null : builder.equal(root.get("year"), year);
    }
    
    public static Specification<Report> hasDepartment(String department) {
        return (root, query, builder) -> 
            department == null ? null : builder.equal(root.get("department"), department);
    }
    
    public static Specification<Report> hasStatus(String status) {
        return (root, query, builder) -> 
            status == null ? null : builder.equal(root.get("status"), status);
    }
}

@Service
public class ReportService {
    
    public List<Report> getReports(Integer year, String department, String status) {
        Specification<Report> spec = Specification.where(ReportSpecification.hasYear(year))
                                                  .and(ReportSpecification.hasDepartment(department))
                                                  .and(ReportSpecification.hasStatus(status));
        
        return reportRepository.findAll(spec);
    }
}
```

Repository:
```java
public interface ReportRepository extends JpaRepository<Report, Long>, JpaSpecificationExecutor<Report> {
}
```

**Search across multiple fields:**
```java
@GetMapping("/reports/search")
public List<Report> searchReports(@RequestParam String keyword) {
    return reportRepository.searchByKeyword(keyword);
}

// Repository
@Query("SELECT r FROM Report r WHERE " +
       "LOWER(r.title) LIKE LOWER(CONCAT('%', :keyword, '%')) OR " +
       "LOWER(r.description) LIKE LOWER(CONCAT('%', :keyword, '%'))")
List<Report> searchByKeyword(@Param("keyword") String keyword);
```

Usage: `GET /api/reports/search?keyword=performance`

---

### **Q47: How do you implement sorting in REST API?**

**Using Pageable (automatic):**
```java
@GetMapping("/reports")
public Page<Report> getReports(Pageable pageable) {
    return reportRepository.findAll(pageable);
}
```

Client can sort using:
```
GET /api/reports?sort=createdDate,desc
GET /api/reports?sort=title,asc
GET /api/reports?sort=year,desc&sort=title,asc  // Multiple sorts
```

**Manual sorting with @RequestParam:**
```java
@GetMapping("/reports")
public List<Report> getReports(
        @RequestParam(defaultValue = "createdDate") String sortBy,
        @RequestParam(defaultValue = "desc") String sortOrder) {
    
    Sort sort = sortOrder.equalsIgnoreCase("asc") 
        ? Sort.by(sortBy).ascending() 
        : Sort.by(sortBy).descending();
    
    return reportRepository.findAll(sort);
}
```

Usage: `GET /api/reports?sortBy=year&sortOrder=desc`

**Custom sorting logic:**
```java
@GetMapping("/employees/top-performers")
public List<Employee> getTopPerformers() {
    return employeeRepository.findAll(
        Sort.by(Sort.Direction.DESC, "performanceScore")
            .and(Sort.by(Sort.Direction.ASC, "name"))
    );
}
```

**Sorting with pagination:**
```java
GET /api/reports?page=0&size=10&sort=year,desc&sort=title,asc
```

Spring automatically handles both pagination and sorting together.

---

### **Q48: How do you implement rate limiting in REST API?**

Rate limiting prevents abuse by limiting requests per user/IP.

**Using Bucket4j library:**

Add dependency:
```xml
<dependency>
    <groupId>com.github.vladimir-bukhtoyarov</groupId>
    <artifactId>bucket4j-core</artifactId>
    <version>7.6.0</version>
</dependency>
```

Create rate limiter:
```java
@Component
public class RateLimitingService {
    
    private final Map<String, Bucket> cache = new ConcurrentHashMap<>();
    
    public Bucket resolveBucket(String key) {
        return cache.computeIfAbsent(key, k -> createNewBucket());
    }
    
    private Bucket createNewBucket() {
        Bandwidth limit = Bandwidth.classic(100, Refill.intervally(100, Duration.ofMinutes(1)));
        return Bucket.builder()
                .addLimit(limit)
                .build();
    }
}
```

Create interceptor:
```java
@Component
public class RateLimitInterceptor implements HandlerInterceptor {
    
    @Autowired
    private RateLimitingService rateLimitingService;
    
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) {
        String key = getClientIP(request);  // Or use username from JWT
        Bucket bucket = rateLimitingService.resolveBucket(key);
        
        if (bucket.tryConsume(1)) {
            return true;  // Allow request
        } else {
            response.setStatus(429);  // Too Many Requests
            return false;  // Block request
        }
    }
    
    private String getClientIP(HttpServletRequest request) {
        String ip = request.getHeader("X-Forwarded-For");
        return (ip != null) ? ip : request.getRemoteAddr();
    }
}
```

Register interceptor:
```java
@Configuration
public class WebConfig implements WebMvcConfigurer {
    
    @Autowired
    private RateLimitInterceptor rateLimitInterceptor;
    
    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(rateLimitInterceptor)
                .addPathPatterns("/api/**");
    }
}
```

Now each IP/user can make only 100 requests per minute. Exceeding this returns 429 status.

**Rate limit by endpoint:**
```java
@RestController
public class AuthController {
    
    private final Bucket loginBucket = Bucket.builder()
        .addLimit(Bandwidth.classic(5, Refill.intervally(5, Duration.ofMinutes(1))))
        .build();
    
    @PostMapping("/api/auth/login")
    public ResponseEntity<?> login(@RequestBody LoginRequest request) {
        if (!loginBucket.tryConsume(1)) {
            return ResponseEntity.status(429).body("Too many login attempts");
        }
        
        // Login logic
    }
}
```

This allows only 5 login attempts per minute to prevent brute force attacks.

---

### **Q49: How do you implement caching in REST API?**

Caching improves performance by storing frequently accessed data in memory.

**Step 1: Add dependency**
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-cache</artifactId>
</dependency>
```

**Step 2: Enable caching**
```java
@SpringBootApplication
@EnableCaching
public class PerformanceTrackApplication {
    public static void main(String[] args) {
        SpringApplication.run(PerformanceTrackApplication.class, args);
    }
}
```

**Step 3: Use @Cacheable annotation**
```java
@Service
public class ReportService {
    
    @Cacheable(value = "reports", key = "#id")
    public Report getReport(Long id) {
        System.out.println("Fetching from database...");
        return reportRepository.findById(id).orElseThrow();
    }
    
    @Cacheable(value = "departmentReports", key = "#department")
    public List<Report> getReportsByDepartment(String department) {
        System.out.println("Fetching from database...");
        return reportRepository.findByDepartment(department);
    }
    
    @CacheEvict(value = "reports", key = "#id")
    public void deleteReport(Long id) {
        reportRepository.deleteById(id);
        // Cache entry is removed
    }
    
    @CachePut(value = "reports", key = "#report.id")
    public Report updateReport(Report report) {
        return reportRepository.save(report);
        // Cache is updated with new value
    }
    
    @CacheEvict(value = {"reports", "departmentReports"}, allEntries = true)
    public void clearAllCaches() {
        // Clear all cache entries
    }
}
```

**How it works:**

First call:
```
GET /api/reports/1
→ Checks cache (not found)
→ Fetches from database
→ Stores in cache
→ Returns result
Output: "Fetching from database..."
```

Second call (within cache time):
```
GET /api/reports/1
→ Checks cache (found!)
→ Returns from cache (no database query)
→ Returns result
Output: (no database call)
```

**Configure cache TTL:**
```properties
spring.cache.type=caffeine
spring.cache.caffeine.spec=maximumSize=1000,expireAfterWrite=10m
```

**Use Redis for distributed caching:**
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
```

```properties
spring.cache.type=redis
spring.redis.host=localhost
spring.redis.port=6379
```

For PerformanceTrack, cache reports, employee data, and analytics that don't change frequently.

---

### **Q50: What is HATEOAS in REST?**

HATEOAS (Hypermedia As The Engine Of Application State) means including links in API responses so clients can discover available actions.

**Without HATEOAS:**
```json
{
  "id": 1,
  "title": "Q1 Performance Report",
  "employeeId": 123
}
```

Client needs to know all URLs beforehand: "To get employee details, I need to call /api/employees/123"

**With HATEOAS:**
```json
{
  "id": 1,
  "title": "Q1 Performance Report",
  "employeeId": 123,
  "_links": {
    "self": {
      "href": "http://localhost:8080/api/reports/1"
    },
    "employee": {
      "href": "http://localhost:8080/api/employees/123"
    },
    "reviews": {
      "href": "http://localhost:8080/api/reports/1/reviews"
    },
    "approve": {
      "href": "http://localhost:8080/api/reports/1/approve"
    }
  }
}
```

Client discovers available actions from response itself.

**Implementation in Spring Boot:**

Add dependency:
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-hateoas</artifactId>
</dependency>
```

Controller:
```java
@GetMapping("/reports/{id}")
public EntityModel<Report> getReport(@PathVariable Long id) {
    Report report = reportService.getReport(id);
    
    EntityModel<Report> resource = EntityModel.of(report);
    
    // Add links
    resource.add(linkTo(methodOn(ReportController.class).getReport(id)).withSelfRel());
    resource.add(linkTo(methodOn(EmployeeController.class).getEmployee(report.getEmployeeId())).withRel("employee"));
    resource.add(linkTo(methodOn(ReviewController.class).getReviewsByReport(id)).withRel("reviews"));
    
    if (report.getStatus().equals("PENDING")) {
        resource.add(linkTo(methodOn(ReportController.class).approveReport(id)).withRel("approve"));
    }
    
    return resource;
}
```

**For PerformanceTrack college project, HATEOAS is optional** - it adds complexity and most real-world REST APIs don't use it. Simple JSON responses are sufficient.

---

**Should I continue with more advanced topics? I can cover:**
- File upload/download in REST APIs
- WebSocket integration
- API security best practices
- Testing REST APIs
- Microservices patterns
- GraphQL basics
- API Gateway patterns

Let me know if you want me to continue!

## **REST API Advanced Topics (Continued)**

### **Q51: How do you implement file upload in REST API?**

Use `MultipartFile` to handle file uploads in Spring Boot.

**Single file upload:**
```java
@RestController
@RequestMapping("/api/files")
public class FileUploadController {
    
    @Value("${file.upload-dir}")
    private String uploadDir;
    
    @PostMapping("/upload")
    public ResponseEntity<FileUploadResponse> uploadFile(
            @RequestParam("file") MultipartFile file) {
        
        // Validate file
        if (file.isEmpty()) {
            return ResponseEntity.badRequest()
                .body(new FileUploadResponse("error", "File is empty"));
        }
        
        // Check file type
        String contentType = file.getContentType();
        if (!contentType.equals("application/pdf") && !contentType.startsWith("image/")) {
            return ResponseEntity.badRequest()
                .body(new FileUploadResponse("error", "Only PDF and images allowed"));
        }
        
        // Check file size (max 10MB)
        if (file.getSize() > 10 * 1024 * 1024) {
            return ResponseEntity.badRequest()
                .body(new FileUploadResponse("error", "File size exceeds 10MB"));
        }
        
        try {
            // Generate unique filename
            String originalFilename = file.getOriginalFilename();
            String extension = originalFilename.substring(originalFilename.lastIndexOf("."));
            String newFilename = UUID.randomUUID().toString() + extension;
            
            // Save file
            Path filePath = Paths.get(uploadDir, newFilename);
            Files.createDirectories(filePath.getParent());
            file.transferTo(filePath.toFile());
            
            // Save metadata to database
            FileMetadata metadata = new FileMetadata();
            metadata.setOriginalName(originalFilename);
            metadata.setStoredName(newFilename);
            metadata.setFileSize(file.getSize());
            metadata.setContentType(contentType);
            metadata.setUploadDate(LocalDateTime.now());
            fileRepository.save(metadata);
            
            return ResponseEntity.ok(new FileUploadResponse(
                "success", 
                "File uploaded successfully", 
                newFilename
            ));
            
        } catch (IOException e) {
            return ResponseEntity.status(HttpStatus.INTERNAL_SERVER_ERROR)
                .body(new FileUploadResponse("error", "File upload failed"));
        }
    }
}
```

**Multiple files upload:**
```java
@PostMapping("/upload-multiple")
public ResponseEntity<List<FileUploadResponse>> uploadMultipleFiles(
        @RequestParam("files") MultipartFile[] files) {
    
    List<FileUploadResponse> responses = new ArrayList<>();
    
    for (MultipartFile file : files) {
        // Process each file similar to single upload
        String filename = saveFile(file);
        responses.add(new FileUploadResponse("success", "Uploaded", filename));
    }
    
    return ResponseEntity.ok(responses);
}
```

**Configure file upload properties:**
```properties
# application.properties
spring.servlet.multipart.max-file-size=10MB
spring.servlet.multipart.max-request-size=20MB
file.upload-dir=/uploads
```

**Frontend usage (React example):**
```javascript
const formData = new FormData();
formData.append('file', selectedFile);

fetch('/api/files/upload', {
  method: 'POST',
  headers: {
    'Authorization': 'Bearer ' + token
  },
  body: formData
})
.then(response => response.json())
.then(data => console.log(data));
```

In PerformanceTrack, use this for uploading evidence documents, profile pictures, or report attachments.

---

### **Q52: How do you implement file download in REST API?**

Return file as `Resource` with appropriate headers.

**Download by filename:**
```java
@GetMapping("/download/{filename}")
public ResponseEntity<Resource> downloadFile(@PathVariable String filename) {
    
    try {
        // Load file
        Path filePath = Paths.get(uploadDir, filename);
        Resource resource = new UrlResource(filePath.toUri());
        
        if (!resource.exists()) {
            return ResponseEntity.notFound().build();
        }
        
        // Get file metadata from database
        FileMetadata metadata = fileRepository.findByStoredName(filename);
        
        // Determine content type
        String contentType = metadata.getContentType();
        if (contentType == null) {
            contentType = "application/octet-stream";
        }
        
        return ResponseEntity.ok()
            .contentType(MediaType.parseMediaType(contentType))
            .header(HttpHeaders.CONTENT_DISPOSITION, 
                   "attachment; filename=\"" + metadata.getOriginalName() + "\"")
            .body(resource);
            
    } catch (Exception e) {
        return ResponseEntity.status(HttpStatus.INTERNAL_SERVER_ERROR).build();
    }
}
```

**Download as inline (display in browser):**
```java
@GetMapping("/view/{filename}")
public ResponseEntity<Resource> viewFile(@PathVariable String filename) {
    // Similar to download but use "inline" instead of "attachment"
    return ResponseEntity.ok()
        .contentType(MediaType.parseMediaType(contentType))
        .header(HttpHeaders.CONTENT_DISPOSITION, 
               "inline; filename=\"" + metadata.getOriginalName() + "\"")
        .body(resource);
}
```

**Stream large files (memory efficient):**
```java
@GetMapping("/stream/{filename}")
public void streamFile(@PathVariable String filename, HttpServletResponse response) {
    
    try {
        Path filePath = Paths.get(uploadDir, filename);
        
        response.setContentType("application/pdf");
        response.setHeader(HttpHeaders.CONTENT_DISPOSITION, 
                          "attachment; filename=\"" + filename + "\"");
        
        InputStream inputStream = Files.newInputStream(filePath);
        OutputStream outputStream = response.getOutputStream();
        
        byte[] buffer = new byte[1024];
        int bytesRead;
        while ((bytesRead = inputStream.read(buffer)) != -1) {
            outputStream.write(buffer, 0, bytesRead);
        }
        
        inputStream.close();
        outputStream.close();
        
    } catch (IOException e) {
        response.setStatus(HttpServletResponse.SC_INTERNAL_SERVER_ERROR);
    }
}
```

**Generate and download report:**
```java
@GetMapping("/reports/{id}/export")
public ResponseEntity<byte[]> exportReport(@PathVariable Long id) {
    
    Report report = reportService.getReport(id);
    
    // Generate PDF
    byte[] pdfBytes = pdfGeneratorService.generateReportPDF(report);
    
    return ResponseEntity.ok()
        .contentType(MediaType.APPLICATION_PDF)
        .header(HttpHeaders.CONTENT_DISPOSITION, 
               "attachment; filename=\"report-" + id + ".pdf\"")
        .body(pdfBytes);
}
```

**Frontend usage:**
```javascript
// Download file
fetch('/api/files/download/abc123.pdf', {
  headers: {
    'Authorization': 'Bearer ' + token
  }
})
.then(response => response.blob())
.then(blob => {
  const url = window.URL.createObjectURL(blob);
  const a = document.createElement('a');
  a.href = url;
  a.download = 'report.pdf';
  a.click();
});
```

---

### **Q53: How do you implement bulk operations in REST API?**

Bulk operations allow processing multiple items in single request.

**Bulk delete:**
```java
@DeleteMapping("/reports/bulk")
public ResponseEntity<BulkOperationResponse> bulkDeleteReports(
        @RequestBody List<Long> reportIds) {
    
    int successCount = 0;
    int failureCount = 0;
    List<String> errors = new ArrayList<>();
    
    for (Long id : reportIds) {
        try {
            reportService.deleteReport(id);
            successCount++;
        } catch (Exception e) {
            failureCount++;
            errors.add("Failed to delete report " + id + ": " + e.getMessage());
        }
    }
    
    return ResponseEntity.ok(new BulkOperationResponse(
        successCount, 
        failureCount, 
        errors
    ));
}
```

Request:
```json
DELETE /api/reports/bulk
Body: [1, 2, 3, 4, 5]
```

Response:
```json
{
  "successCount": 4,
  "failureCount": 1,
  "errors": ["Failed to delete report 3: Not found"]
}
```

**Bulk create:**
```java
@PostMapping("/goals/bulk")
public ResponseEntity<BulkCreateResponse> bulkCreateGoals(
        @RequestBody List<Goal> goals) {
    
    List<Goal> createdGoals = new ArrayList<>();
    List<String> errors = new ArrayList<>();
    
    for (Goal goal : goals) {
        try {
            // Validate
            if (goal.getTitle() == null || goal.getTitle().isEmpty()) {
                errors.add("Goal title is required");
                continue;
            }
            
            Goal saved = goalService.createGoal(goal);
            createdGoals.add(saved);
            
        } catch (Exception e) {
            errors.add("Failed to create goal: " + e.getMessage());
        }
    }
    
    return ResponseEntity.ok(new BulkCreateResponse(createdGoals, errors));
}
```

**Bulk update:**
```java
@PutMapping("/reviews/bulk-approve")
public ResponseEntity<BulkOperationResponse> bulkApproveReviews(
        @RequestBody BulkApprovalRequest request) {
    
    int approved = 0;
    List<String> errors = new ArrayList<>();
    
    for (Long reviewId : request.getReviewIds()) {
        try {
            reviewService.approveReview(reviewId, request.getApprovedBy());
            approved++;
        } catch (Exception e) {
            errors.add("Review " + reviewId + ": " + e.getMessage());
        }
    }
    
    return ResponseEntity.ok(new BulkOperationResponse(approved, errors.size(), errors));
}
```

**Batch processing with transactions:**
```java
@Transactional
@PostMapping("/employees/bulk-import")
public ResponseEntity<ImportResponse> importEmployees(
        @RequestBody List<EmployeeDTO> employees) {
    
    try {
        List<Employee> savedEmployees = new ArrayList<>();
        
        for (EmployeeDTO dto : employees) {
            Employee employee = new Employee();
            // Map DTO to entity
            employee.setName(dto.getName());
            employee.setEmail(dto.getEmail());
            
            savedEmployees.add(employeeRepository.save(employee));
        }
        
        return ResponseEntity.ok(new ImportResponse(
            "success", 
            savedEmployees.size() + " employees imported"
        ));
        
    } catch (Exception e) {
        // Transaction rolls back if any employee fails
        return ResponseEntity.status(HttpStatus.INTERNAL_SERVER_ERROR)
            .body(new ImportResponse("error", "Import failed: " + e.getMessage()));
    }
}
```

In PerformanceTrack, use bulk operations for admin tasks like deleting multiple old reports or importing employee data.

---

### **Q54: How do you implement async processing in REST API?**

Async processing allows API to return immediately while processing continues in background.

**Enable async support:**
```java
@SpringBootApplication
@EnableAsync
public class PerformanceTrackApplication {
    public static void main(String[] args) {
        SpringApplication.run(PerformanceTrackApplication.class, args);
    }
}
```

**Configure thread pool:**
```java
@Configuration
public class AsyncConfig {
    
    @Bean
    public Executor taskExecutor() {
        ThreadPoolTaskExecutor executor = new ThreadPoolTaskExecutor();
        executor.setCorePoolSize(5);
        executor.setMaxPoolSize(10);
        executor.setQueueCapacity(100);
        executor.setThreadNamePrefix("async-");
        executor.initialize();
        return executor;
    }
}
```

**Async method:**
```java
@Service
public class ReportService {
    
    @Async
    public CompletableFuture<Report> generateLargeReport(Long employeeId) {
        System.out.println("Starting report generation in: " + Thread.currentThread().getName());
        
        // Simulate long-running task
        try {
            Thread.sleep(5000);  // 5 seconds
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        
        Report report = new Report();
        report.setEmployeeId(employeeId);
        report.setGeneratedAt(LocalDateTime.now());
        
        return CompletableFuture.completedFuture(report);
    }
    
    @Async
    public void sendEmailNotification(String email, String message) {
        // Send email asynchronously
        emailService.send(email, message);
    }
}
```

**Controller with async:**
```java
@GetMapping("/reports/generate-async/{employeeId}")
public CompletableFuture<ResponseEntity<Report>> generateReportAsync(
        @PathVariable Long employeeId) {
    
    return reportService.generateLargeReport(employeeId)
        .thenApply(report -> ResponseEntity.ok(report));
}
```

**Fire and forget pattern:**
```java
@PostMapping("/reviews")
public ResponseEntity<Review> createReview(@RequestBody Review review) {
    Review saved = reviewService.saveReview(review);
    
    // Send notification asynchronously (don't wait)
    notificationService.sendEmailNotification(
        review.getEmployeeEmail(), 
        "Your review has been submitted"
    );
    
    return ResponseEntity.status(HttpStatus.CREATED).body(saved);
}
```

**Long-running task with status tracking:**
```java
@RestController
public class AsyncTaskController {
    
    private Map<String, String> taskStatus = new ConcurrentHashMap<>();
    
    @PostMapping("/tasks/process-data")
    public ResponseEntity<TaskResponse> startProcessing(@RequestBody DataRequest request) {
        String taskId = UUID.randomUUID().toString();
        taskStatus.put(taskId, "PROCESSING");
        
        // Start async processing
        dataProcessingService.processData(request, taskId);
        
        return ResponseEntity.accepted()
            .body(new TaskResponse(taskId, "Task started"));
    }
    
    @GetMapping("/tasks/{taskId}/status")
    public ResponseEntity<TaskStatus> getTaskStatus(@PathVariable String taskId) {
        String status = taskStatus.get(taskId);
        
        if (status == null) {
            return ResponseEntity.notFound().build();
        }
        
        return ResponseEntity.ok(new TaskStatus(taskId, status));
    }
}

@Service
public class DataProcessingService {
    
    @Async
    public void processData(DataRequest request, String taskId) {
        try {
            // Long processing
            Thread.sleep(30000);  // 30 seconds
            
            taskStatus.put(taskId, "COMPLETED");
        } catch (Exception e) {
            taskStatus.put(taskId, "FAILED");
        }
    }
}
```

Client workflow:
```
1. POST /api/tasks/process-data
   Response: {"taskId": "abc123", "message": "Task started"}

2. GET /api/tasks/abc123/status (poll every few seconds)
   Response: {"taskId": "abc123", "status": "PROCESSING"}

3. GET /api/tasks/abc123/status (after 30 seconds)
   Response: {"taskId": "abc123", "status": "COMPLETED"}
```

---

### **Q55: How do you handle concurrent requests in REST API?**

**Problem: Race condition**
```java
// Two users try to approve same review simultaneously
User1: GET /api/reviews/1 → status = "PENDING"
User2: GET /api/reviews/1 → status = "PENDING"
User1: PUT /api/reviews/1/approve → status = "APPROVED"
User2: PUT /api/reviews/1/approve → status = "APPROVED" (approves already approved review!)
```

**Solution 1: Optimistic locking with @Version**
```java
@Entity
public class Review {
    @Id
    private Long id;
    
    private String status;
    
    @Version  // Hibernate manages version automatically
    private Integer version;
    
    // getters, setters
}

@Service
public class ReviewService {
    
    @Transactional
    public Review approveReview(Long id) {
        Review review = reviewRepository.findById(id)
            .orElseThrow(() -> new ResourceNotFoundException("Review not found"));
        
        if (!review.getStatus().equals("PENDING")) {
            throw new IllegalStateException("Review already processed");
        }
        
        review.setStatus("APPROVED");
        return reviewRepository.save(review);
        // If version changed (another user updated), throws OptimisticLockingFailureException
    }
}

@RestControllerAdvice
public class GlobalExceptionHandler {
    
    @ExceptionHandler(OptimisticLockingFailureException.class)
    public ResponseEntity<ErrorResponse> handleOptimisticLock(OptimisticLockingFailureException ex) {
        return ResponseEntity.status(HttpStatus.CONFLICT)
            .body(new ErrorResponse("CONFLICT", "Review was modified by another user. Please refresh and try again."));
    }
}
```

**Solution 2: Pessimistic locking**
```java
public interface ReviewRepository extends JpaRepository<Review, Long> {
    
    @Lock(LockModeType.PESSIMISTIC_WRITE)
    @Query("SELECT r FROM Review r WHERE r.id = :id")
    Optional<Review> findByIdWithLock(@Param("id") Long id);
}

@Service
public class ReviewService {
    
    @Transactional
    public Review approveReview(Long id) {
        // This locks the row in database until transaction completes
        Review review = reviewRepository.findByIdWithLock(id)
            .orElseThrow(() -> new ResourceNotFoundException("Review not found"));
        
        review.setStatus("APPROVED");
        return reviewRepository.save(review);
    }
}
```

**Solution 3: Synchronized method (single server only)**
```java
@Service
public class ReviewService {
    
    private final Map<Long, Object> locks = new ConcurrentHashMap<>();
    
    public Review approveReview(Long id) {
        Object lock = locks.computeIfAbsent(id, k -> new Object());
        
        synchronized(lock) {
            Review review = reviewRepository.findById(id).orElseThrow();
            
            if (!review.getStatus().equals("PENDING")) {
                throw new IllegalStateException("Review already processed");
            }
            
            review.setStatus("APPROVED");
            return reviewRepository.save(review);
        }
    }
}
```

**Solution 4: Distributed lock with Redis (for multiple servers)**
```java
@Service
public class ReviewService {
    
    @Autowired
    private RedissonClient redisson;
    
    public Review approveReview(Long id) {
        RLock lock = redisson.getLock("review-lock-" + id);
        
        try {
            lock.lock(10, TimeUnit.SECONDS);
            
            Review review = reviewRepository.findById(id).orElseThrow();
            
            if (!review.getStatus().equals("PENDING")) {
                throw new IllegalStateException("Review already processed");
            }
            
            review.setStatus("APPROVED");
            return reviewRepository.save(review);
            
        } finally {
            lock.unlock();
        }
    }
}
```

For PerformanceTrack, use **optimistic locking** (Solution 1) - it's simplest and works well for most cases.

---

### **Q56: How do you implement API health checks?**

Health checks let you monitor if API is running properly.

**Using Spring Boot Actuator:**

Add dependency:
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

Configure:
```properties
# application.properties
management.endpoints.web.exposure.include=health,info,metrics
management.endpoint.health.show-details=always
```

**Built-in health endpoint:**
```
GET /actuator/health
```

Response:
```json
{
  "status": "UP",
  "components": {
    "db": {
      "status": "UP",
      "details": {
        "database": "MySQL",
        "validationQuery": "isValid()"
      }
    },
    "diskSpace": {
      "status": "UP",
      "details": {
        "total": 500000000000,
        "free": 200000000000,
        "threshold": 10485760
      }
    }
  }
}
```

**Custom health indicator:**
```java
@Component
public class ExternalServiceHealthIndicator implements HealthIndicator {
    
    @Autowired
    private RestTemplate restTemplate;
    
    @Override
    public Health health() {
        try {
            // Check if external service is reachable
            String url = "https://external-api.com/ping";
            ResponseEntity<String> response = restTemplate.getForEntity(url, String.class);
            
            if (response.getStatusCode() == HttpStatus.OK) {
                return Health.up()
                    .withDetail("externalService", "Available")
                    .build();
            } else {
                return Health.down()
                    .withDetail("externalService", "Returned " + response.getStatusCode())
                    .build();
            }
            
        } catch (Exception e) {
            return Health.down()
                .withDetail("externalService", "Unavailable")
                .withDetail("error", e.getMessage())
                .build();
        }
    }
}
```

**Database connection health:**
```java
@Component
public class DatabaseHealthIndicator implements HealthIndicator {
    
    @Autowired
    private DataSource dataSource;
    
    @Override
    public Health health() {
        try (Connection conn = dataSource.getConnection()) {
            boolean isValid = conn.isValid(2);  // 2 second timeout
            
            if (isValid) {
                return Health.up()
                    .withDetail("database", "Connected")
                    .build();
            } else {
                return Health.down()
                    .withDetail("database", "Connection invalid")
                    .build();
            }
        } catch (Exception e) {
            return Health.down()
                .withDetail("database", "Connection failed")
                .withDetail("error", e.getMessage())
                .build();
        }
    }
}
```

**Custom simple health endpoint:**
```java
@RestController
@RequestMapping("/api/health")
public class HealthController {
    
    @GetMapping
    public ResponseEntity<HealthStatus> health() {
        return ResponseEntity.ok(new HealthStatus(
            "UP", 
            "PerformanceTrack API is running",
            LocalDateTime.now()
        ));
    }
    
    @GetMapping("/ready")
    public ResponseEntity<String> readiness() {
        // Check if application is ready to serve requests
        boolean dbConnected = checkDatabaseConnection();
        boolean cacheReady = checkCacheConnection();
        
        if (dbConnected && cacheReady) {
            return ResponseEntity.ok("READY");
        } else {
            return ResponseEntity.status(HttpStatus.SERVICE_UNAVAILABLE).body("NOT_READY");
        }
    }
    
    @GetMapping("/live")
    public ResponseEntity<String> liveness() {
        // Check if application is alive (basic check)
        return ResponseEntity.ok("ALIVE");
    }
}
```

In production, monitoring tools (like Kubernetes, AWS CloudWatch) call these endpoints to check application health.

---

### **Q57: How do you implement request/response logging in REST API?**

**Method 1: Using Filter**
```java
@Component
public class RequestResponseLoggingFilter extends OncePerRequestFilter {
    
    private static final Logger logger = LoggerFactory.getLogger(RequestResponseLoggingFilter.class);
    
    @Override
    protected void doFilterInternal(HttpServletRequest request, 
                                   HttpServletResponse response, 
                                   FilterChain filterChain) throws ServletException, IOException {
        
        // Log request
        logger.info("Request: {} {} from {}",
            request.getMethod(),
            request.getRequestURI(),
            request.getRemoteAddr()
        );
        
        // Log headers
        Enumeration<String> headerNames = request.getHeaderNames();
        while (headerNames.hasMoreElements()) {
            String headerName = headerNames.nextElement();
            if (!headerName.equals("Authorization")) {  // Don't log sensitive headers
                logger.debug("Header: {} = {}", headerName, request.getHeader(headerName));
            }
        }
        
        long startTime = System.currentTimeMillis();
        
        // Continue filter chain
        filterChain.doFilter(request, response);
        
        // Log response
        long duration = System.currentTimeMillis() - startTime;
        logger.info("Response: {} {} - Status: {} - Duration: {}ms",
            request.getMethod(),
            request.getRequestURI(),
            response.getStatus(),
            duration
        );
    }
}
```

**Method 2: Using Interceptor**
```java
@Component
public class LoggingInterceptor implements HandlerInterceptor {
    
    private static final Logger logger = LoggerFactory.getLogger(LoggingInterceptor.class);
    
    @Override
    public boolean preHandle(HttpServletRequest request, 
                            HttpServletResponse response, 
                            Object handler) {
        
        request.setAttribute("startTime", System.currentTimeMillis());
        
        logger.info("[{}] {} {} - User: {}",
            request.getMethod(),
            request.getRequestURI(),
            request.getQueryString() != null ? "?" + request.getQueryString() : "",
            getCurrentUsername()
        );
        
        return true;
    }
    
    @Override
    public void afterCompletion(HttpServletRequest request, 
                               HttpServletResponse response, 
                               Object handler, 
                               Exception ex) {
        
        long startTime = (Long) request.getAttribute("startTime");
        long duration = System.currentTimeMillis() - startTime;
        
        logger.info("[{}] {} - Status: {} - Duration: {}ms",
            request.getMethod(),
            request.getRequestURI(),
            response.getStatus(),
            duration
        );
        
        if (ex != null) {
            logger.error("Request failed with exception", ex);
        }
    }
    
    private String getCurrentUsername() {
        Authentication auth = SecurityContextHolder.getContext().getAuthentication();
        return auth != null ? auth.getName() : "anonymous";
    }
}

@Configuration
public class WebConfig implements WebMvcConfigurer {
    
    @Autowired
    private LoggingInterceptor loggingInterceptor;
    
    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(loggingInterceptor)
                .addPathPatterns("/api/**");
    }
}
```

**Method 3: Using AOP**
```java
@Aspect
@Component
public class ApiLoggingAspect {
    
    private static final Logger logger = LoggerFactory.getLogger(ApiLoggingAspect.class);
    
    @Around("@annotation(org.springframework.web.bind.annotation.GetMapping) || " +
            "@annotation(org.springframework.web.bind.annotation.PostMapping) || " +
            "@annotation(org.springframework.web.bind.annotation.PutMapping) || " +
            "@annotation(org.springframework.web.bind.annotation.DeleteMapping)")
    public Object logApiCall(ProceedingJoinPoint joinPoint) throws Throwable {
        
        String methodName = joinPoint.getSignature().getName();
        String className = joinPoint.getTarget().getClass().getSimpleName();
        Object[] args = joinPoint.getArgs();
        
        logger.info("API Call: {}.{} with args: {}", 
            className, methodName, Arrays.toString(args));
        
        long startTime = System.currentTimeMillis();
        
        try {
            Object result = joinPoint.proceed();
            
            long duration = System.currentTimeMillis() - startTime;
            logger.info("API Success: {}.{} - Duration: {}ms", 
                className, methodName, duration);
            
            return result;
            
        } catch (Exception e) {
            long duration = System.currentTimeMillis() - startTime;
            logger.error("API Failed: {}.{} - Duration: {}ms - Error: {}", 
                className, methodName, duration, e.getMessage());
            throw e;
        }
    }
}
```

**Log output example:**
```
2024-12-10 10:30:15 INFO  [GET] /api/reports?year=2024 - User: john
2024-12-10 10:30:15 INFO  API Call: ReportController.getReports with args: [2024]
2024-12-10 10:30:16 INFO  API Success: ReportController.getReports - Duration: 245ms
2024-12-10 10:30:16 INFO  [GET] /api/reports - Status: 200 - Duration: 246ms
```

For PerformanceTrack, use **Method 2 (Interceptor)** - it's cleaner and gives you control over what to log.

---

### **Q58: How do you implement API throttling/rate limiting per user?**

Limit requests based on authenticated user instead of IP address.

**Using Bucket4j with user-based keys:**
```java
@Component
public class UserRateLimitingService {
    
    private final Map<String, Bucket> userBuckets = new ConcurrentHashMap<>();
    
    public Bucket getUserBucket(String username) {
        return userBuckets.computeIfAbsent(username, this::createBucket);
    }
    
    private Bucket createBucket(String username) {
        // Different limits for different roles
        User user = userRepository.findByUsername(username);
        
        int requestsPerMinute;
        if (user.getRole().equals("ADMIN")) {
            requestsPerMinute = 1000;  // Admins get more
        } else if (user.getRole().equals("MANAGER")) {
            requestsPerMinute = 500;
        } else {
            requestsPerMinute = 100;  // Regular employees
        }
        
        Bandwidth limit = Bandwidth.classic(
            requestsPerMinute, 
            Refill.intervally(requestsPerMinute, Duration.ofMinutes(1))
        );
        
        return Bucket.builder().addLimit(limit).build();
    }
    
    public boolean allowRequest(String username) {
        Bucket bucket = getUserBucket(username);
        return bucket.tryConsume(1);
    }
}
```

**Interceptor:**
```java
@Component
public class UserRateLimitInterceptor implements HandlerInterceptor {
    
    @Autowired
    private UserRateLimitingService rateLimitingService;
    
    @Override
    public boolean preHandle(HttpServletRequest request, 
                            HttpServletResponse response, 
                            Object handler) throws Exception {
        
        // Get username from SecurityContext
        Authentication auth = SecurityContextHolder.getContext().getAuthentication();
        
        if (auth != null && auth.isAuthenticated()) {
            String username = auth.getName();
            
            if (!rateLimitingService.allowRequest(username)) {
                response.setStatus(429);  // Too Many Requests
                response.getWriter().write(
                    "{\"error\": \"RATE_LIMIT_EXCEEDED\", " +
                    "\"message\": \"Too many requests. Please try again later.\"}"
                );
                return false;
            }
        }
        
        return true;
    }
}
```

**Custom annotation for specific endpoints:**
```java
@Target(ElementType.METHOD)
@Retention(RetentionPolicy.RUNTIME)
public @interface RateLimited {
    int requests() default 10;
    int perSeconds() default 60;
}

@Aspect
@Component
public class RateLimitAspect {
    
    @Around("@annotation(rateLimited)")
    public Object checkRateLimit(ProceedingJoinPoint joinPoint, RateLimited rateLimited) throws Throwable {
        
        String username = SecurityContextHolder.getContext()
                                              .getAuthentication()
                                              .getName();
        
        String key = username + ":" + joinPoint.getSignature().getName();
        
        // Check rate limit specific to this endpoint
        if (!isAllowed(key, rateLimited.requests(), rateLimited.perSeconds())) {
            throw new RateLimitExceededException("Too many requests to this endpoint");
        }
        
        return joinPoint.proceed();
    }
}

// Usage in controller
@RateLimited(requests = 5, perSeconds = 60)
@PostMapping("/reports/generate")
public Report generateReport(@RequestBody ReportRequest request) {
    return reportService.generateReport(request);
}
```

**Response with rate limit headers:**
```java
@Override
public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) {
    String username = getCurrentUsername();
    Bucket bucket = rateLimitingService.getUserBucket(username);
    
    ConsumptionProbe probe = bucket.tryConsumeAndReturnRemaining(1);
    
    if (probe.isConsumed()) {
        // Add headers to show remaining requests
        response.addHeader("X-RateLimit-Limit", "100");
        response.addHeader("X-RateLimit-Remaining", String.valueOf(probe.getRemainingTokens()));
        response.addHeader("X-RateLimit-Reset", String.valueOf(probe.getNanosToWaitForRefill() / 1_000_000_000));
        return true;
    } else {
        response.setStatus(429);
        response.addHeader("X-RateLimit-Limit", "100");
        response.addHeader("X-RateLimit-Remaining", "0");
        response.addHeader("Retry-After", String.valueOf(probe.getNanosToWaitForRefill() / 1_000_000_000));
        return false;
    }
}
```

Client sees headers:
```
X-RateLimit-Limit: 100
X-RateLimit-Remaining: 87
X-RateLimit-Reset: 45
```

This tells client: "You have 87 requests remaining, resets in 45 seconds"

---

**Should I continue with more topics like WebSocket, GraphQL basics, Microservices patterns, or Testing REST APIs? Let me know!**