# Project Summary: HRMS (Human Resource Management System)

A Spring Boot RESTful backend application engineered for human resource workflows—including employer management, job postings, candidate registrations, and application tracking.

---

## 🛠️ How This Project Was Built

### Architecture & Engineering Design
The application follows a clean, enterprise-grade **n-tier layered architecture**:

```
[ Client / Postman ]
        │
        ▼
[ Controller Layer ]      REST endpoints, request validation (@Valid, Jakarta Validation)
        │
        ▼
[ Service Layer ]         Business logic, rules, BCrypt hashing, and DTO mapping
        │
        ▼
[ Repository Layer ]      Spring Data JPA / Hibernate Data Access Objects (DAOs)
        │
        ▼
[ Database ]              PostgreSQL (Production/Local) / H2 In-Memory (Test Suite)
```

* **Core Stack**: Java 17+, Spring Boot 3.2.2, Spring Data JPA / Hibernate, PostgreSQL, H2 (test-scope).
* **Pattern Implementation**:
  * **DTO & Request/Response Pattern**: Prevents entity exposure and separates persistence models from API contracts.
  * **Standardized Result Packaging**: Unified generic response envelope (`Result`, `DataResult<T>`, `SuccessDataResult`, `ErrorDataResult`).
  * **Security**: Password encoding using Spring Security Crypto's `BCryptPasswordEncoder`.
  * **Global Exception Handling**: Centralized error interceptor (`@ControllerAdvice`) mapping validation and business errors to clean HTTP status codes.

---

## ⚡ 3 Key Challenges & Solutions

### 1. Modern JDK Annotation Processing & Lombok Compatibility
* **Challenge**: When building on modern Java environments (Java 21+ / 26), compilation broke with `Fatal error compiling: java.lang.ExceptionInInitializerError: com.sun.tools.javac.code.TypeTag :: UNKNOWN`, causing missing getters/setters across all models.
* **Solution**: Configured `maven-compiler-plugin` explicitly with `<annotationProcessorPaths>` pointing to Lombok and updated the Lombok processor version to `1.18.38`, ensuring compatibility with the compiler AST internals without code modifications.

### 2. Isolated, Zero-Dependency Automated Testing
* **Challenge**: Automated unit and integration tests attempted to hit the live PostgreSQL database on `localhost:5432`, failing the build when no external database instance was running.
* **Solution**: Established a dedicated test environment using an isolated in-memory H2 database via [application-test.properties](file:///d:/Web%20Dev/java/hmrs-app/src/test/resources/application-test.properties) and enabled `@ActiveProfiles("test")` across the test suite. All 27 unit and MockMvc integration tests now run reliably in CI/CD without external database dependencies.

### 3. Business Integrity & Relational Constraint Enforcement
* **Challenge**: Enforcing strict real-world business constraints (preventing duplicate applicant emails, unique national identification IDs, duplicate job position titles, and duplicate applications for the same job posting).
* **Solution**: Applied composite database unique constraints (`@Table(uniqueConstraints = ...)`), combined with proactive pre-persistence service checks and a global `@ExceptionHandler(ConstraintViolationException.class)` handler to deliver actionable error responses to clients.

---

## 🌟 Unique Selling Proposition (USP)

> **Predictable, Secure, and Production-Ready Backend Architecture**

1. **Standardized API Envelope**: Every single endpoint returns a predictable and consistent contract structure (`success`, `message`, `data`), simplifying frontend and mobile client integration.
2. **100% Automated Test Coverage for Critical Paths**: Backed by **27 automated tests** (JUnit 5 + MockMvc) verifying database constraints, service logic, and REST controllers with zero flaky external dependencies.
3. **Enterprise Domain Integrity**: Fully validated domain boundary using Jakarta Bean Validation and BCrypt password encryption out-of-the-box.
