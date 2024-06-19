### Task: Building a Comprehensive RESTful API for a Library Management System

 

#### Objective:

Create a RESTful API using Java or Kotlin (preferably with Spring Boot) to manage a library system. The API should support advanced CRUD operations, user authentication, and search functionality.

 

#### Requirements:

1.**Endpoints:**

**User Registration and Authentication:**

**POST** `/api/users/register`

Request Body: JSON containing `username`, `password`

**POST** `/api/users/login`

Request Body: JSON containing `username`, `password`

**Book Management (authenticated users only):**

**POST** `/api/books`

Request Body: JSON containing `title`, `author`, `isbn`, and `publishedDate`

**GET** `/api/books`

**GET** `/api/books/{id}`

**PUT** `/api/books/{id}`

Request Body: JSON containing `title`, `author`, `isbn`, and `publishedDate`

**DELETE** `/api/books/{id}`

**Book Search:**

**GET** `/api/books/search?query={query}`

Query can search by title, author, or ISBN.

 

2.**Data Model:**

**User**: Each user has `id`, `username`, and `password` (stored securely).

**Book**: Each book has `id`, `title`, `author`, `isbn`, and `publishedDate`.

 

3.**Authentication:**

Use JWT (JSON Web Token) for user authentication.

Secure endpoints to allow access only to authenticated users.

 

4.**Persistence:**

Use an in-memory database (like H2) for the sake of simplicity.

 

5.**Error Handling:**

Return appropriate HTTP status codes and error messages for scenarios like:

Resource not found

Invalid input data

Unauthorized access

 

6.**Testing:**

Write unit tests for service layer.

Write integration tests for the REST endpoints.

 

7.**Documentation:**

Provide clear instructions on how to run the application.

Provide sample JSON requests for each endpoint.

Include API documentation using Swagger or similar tools.

 

#### Bonus (Optional):

Implement pagination for the list of books.

Implement role-based access control (e.g., admin vs. regular user).

 

### Deliverables:

A zip file or a GitHub repository link containing:

Source code of the application.

README file with instructions on how to run the application and examples of requests/responses.

Tests for the implemented functionality.

 

### Evaluation Criteria:

Code quality and structure

Proper use of REST principles

Correctness and completeness of the implementation

Error handling and validation

Security implementation

Clarity of the documentation

Test coverage and quality

---

### Example Implementation Guide

 

1.**Project Setup:**

Use Spring Initializr to set up a new Spring Boot project with dependencies for Web, Security, JPA, and H2 Database.

 

2.**Define the Entities:**

```java

   @Entity

   public class User {

       @Id

       @GeneratedValue(strategy = GenerationType.IDENTITY)

       private Long id;

       private String username;

       private String password; // Store securely

       // Getters and setters

   }

 

   @Entity

   public class Book {

       @Id

       @GeneratedValue(strategy = GenerationType.IDENTITY)

       private Long id;

       private String title;

       private String author;

       private String isbn;

       private LocalDate publishedDate;

       // Getters and setters

   }

   ```

   

3.**Create the Repositories:**

```java

   public interface UserRepository extends JpaRepository<User, Long> {

       Optional<User> findByUsername(String username);

   }

 

   public interface BookRepository extends JpaRepository<Book, Long> {

       List<Book> findByTitleContainingOrAuthorContainingOrIsbnContaining(String title, String author, String isbn);

   }

   ```



4.**Implement the Service Layer:**

```java

   @Service

   public class UserService {

       @Autowired

       private UserRepository userRepository;

 

       // Registration and authentication logic

   }

 

   @Service

   public class BookService {

       @Autowired

       private BookRepository bookRepository;

 

       // CRUD operations and search logic

   }

   ```



5.**Create the Controllers:**

```java

   @RestController

   @RequestMapping("/api/users")

   public class UserController {

       @Autowired

       private UserService userService;

 

       // Registration and login endpoints

   }

 

   @RestController

   @RequestMapping("/api/books")

   @PreAuthorize("isAuthenticated()")

   public class BookController {

       @Autowired

       private BookService bookService;

 

       // Book CRUD and search endpoints

   }

   ```



6.**Configure Security:**

```java

   @Configuration

   public class SecurityConfig extends WebSecurityConfigurerAdapter {

       @Override

       protected void configure(HttpSecurity http) throws Exception {

           http.csrf().disable()

               .authorizeRequests()

               .antMatchers("/api/users/**").permitAll()

               .anyRequest().authenticated()

               .and()

               .sessionManagement().sessionCreationPolicy(SessionCreationPolicy.STATELESS);

 

           http.addFilter(new JwtAuthenticationFilter(authenticationManager()));

           http.addFilter(new JwtAuthorizationFilter(authenticationManager()));

       }

   }

   ```



7.**Error Handling:**

```java

   @ControllerAdvice

   public class GlobalExceptionHandler {

       @ExceptionHandler(EntityNotFoundException.class)

       public ResponseEntity<String> handleNotFound(EntityNotFoundException ex) {

           return new ResponseEntity<>(ex.getMessage(), HttpStatus.NOT_FOUND);

       }

 

       @ExceptionHandler(MethodArgumentNotValidException.class)

       public ResponseEntity<String> handleValidationExceptions(MethodArgumentNotValidException ex) {

           return new ResponseEntity<>("Invalid input", HttpStatus.BAD_REQUEST);

       }

 

       @ExceptionHandler(AccessDeniedException.class)

       public ResponseEntity<String> handleAccessDenied(AccessDeniedException ex) {

           return new ResponseEntity<>("Access Denied", HttpStatus.FORBIDDEN);

       }

   }

   ```

   

8.**Testing:**

Use JUnit and Mockito for unit tests.

Use Spring Boot Test for integration tests.
