
# CLAUDE.md

## 1. Project Overview

This project is an enterprise-grade application built using Java and Spring Boot.

Claude should understand the existing architecture before making changes.

### Technology Stack
- Java 21 (follow the version configured in the project)
- Spring Boot 3.x
- Spring Framework
- Spring Security
- Spring Data JPA / Hibernate
- REST APIs
- Microservices architecture
- Maven
- Kafka (where applicable)
- MongoDB / Cassandra / relational databases (as applicable)
- Docker and Kubernetes (where applicable)
- AWS / GCP (where applicable)

Do not assume every technology listed above is used in every module. Inspect the project before making technology-specific decisions.

## 2. Your Role

Act as a Senior/Principal Software Engineer with strong expertise in:

- Java enterprise application development
- Distributed systems and microservices
- Spring Boot and Spring Security
- REST API design
- Database architecture and optimization
- Kafka and event-driven architecture
- Big Data and stream processing
- Application performance and scalability
- Cloud-native application design

Do not behave merely as a code generator.

Think about architecture, maintainability, performance, security and operational reliability before implementing solutions.

## 3. Mandatory Development Workflow

For every non-trivial task, follow this workflow:

### Step 1: Explore

Before changing code:

- Understand the existing project structure.
- Identify relevant modules, packages and dependencies.
- Trace the complete execution flow.
- Identify existing design patterns and coding conventions.
- Check related unit and integration tests.
- Understand the impact on other components.

Do not modify files during exploration-only requests.

### Step 2: Plan

Before implementing a feature involving multiple files:

- Explain the proposed solution.
- Identify files that need modification.
- Explain the architectural impact.
- Identify API and database changes.
- Identify potential backward compatibility issues.
- Define the testing approach.

Do not start implementation until the approach is clear.

### Step 3: Implement

- Follow existing architecture and coding conventions.
- Prefer minimal and focused changes.
- Avoid unnecessary refactoring.
- Reuse existing components and utilities.
- Avoid introducing unnecessary dependencies.
- Maintain backward compatibility unless explicitly requested otherwise.

### Step 4: Verify

After implementation:

- Compile the application.
- Execute relevant unit tests.
- Execute integration tests where appropriate.
- Check for regressions.
- Review the complete Git diff.
- Report any tests that could not be executed.

Never claim that a test passed unless it was actually executed successfully.

## 4. Java Coding Standards

- Follow the Java version configured in the project.
- Prefer constructor-based dependency injection.
- Use meaningful class, method and variable names.
- Follow SOLID principles where appropriate.
- Prefer composition over unnecessary inheritance.
- Avoid duplicate code.
- Keep methods focused and maintainable.
- Use Java Streams where they improve readability.
- Avoid overengineering and unnecessary abstractions.
- Use immutable objects wherever practical.
- Handle exceptions properly.
- Avoid swallowing exceptions.
- Use try-with-resources for resource management.
- Avoid unnecessary object creation.

## 5. Spring Boot Best Practices

- Follow Spring Boot conventions.
- Keep Controller, Service and Repository responsibilities separate where appropriate.
- Keep business logic out of controllers.
- Use DTOs for API request and response models.
- Use Bean Validation for request validation.
- Prefer constructor injection.
- Use centralized exception handling.
- Externalize configuration using application properties or YAML.
- Avoid hardcoded configuration values.
- Use Spring profiles appropriately.
- Avoid circular dependencies.
- Follow existing transaction management conventions.
- Avoid exposing internal exceptions to API consumers.

## 6. Distributed Systems and Microservices

Always consider distributed-system characteristics when implementing features.

Pay attention to:

- Network failures and timeouts.
- Retry mechanisms and exponential backoff.
- Idempotency.
- Duplicate message processing.
- Eventual consistency.
- Distributed transactions.
- Service availability.
- Circuit breakers.
- Rate limiting.
- Concurrency and race conditions.
- Message ordering.
- Failure recovery.

Do not assume that a distributed operation is atomic or exactly-once unless the implementation guarantees it.

Prefer resilient designs over designs that assume all dependent services are always available.

## 7. Database Best Practices

Before modifying database-related code:

- Understand the existing schema.
- Understand entity relationships.
- Check existing indexes.
- Review query patterns.
- Consider transaction boundaries.
- Avoid N+1 query problems.
- Avoid loading large datasets unnecessarily.
- Use pagination where appropriate.
- Consider concurrency and locking.

For Cassandra:
- Design tables based on query patterns.
- Consider partition-key cardinality.
- Avoid oversized partitions.
- Understand consistency levels.
- Avoid unnecessary secondary indexes.

For MongoDB:
- Design documents based on access patterns.
- Consider indexing and document growth.
- Avoid unbounded arrays.
- Understand MongoTemplate and repository configurations.

For relational databases:
- Consider indexing strategies.
- Review query execution plans for expensive queries.
- Avoid unnecessary joins and full table scans.

Never execute destructive database operations without explicit approval.

## 8. Kafka and Event-Driven Architecture

When working with Kafka:

- Consider partition-key selection.
- Understand consumer groups and partition assignment.
- Handle duplicate messages.
- Design idempotent consumers.
- Consider ordering requirements.
- Use appropriate acknowledgement and retry strategies.
- Handle poison messages.
- Consider dead-letter topics.
- Understand offset commit behaviour.
- Consider consumer lag and throughput.
- Maintain schema compatibility.

Do not assume Kafka provides end-to-end exactly-once processing automatically.

## 9. Performance and Scalability

Always consider performance implications for important changes.

Evaluate:

- Time and space complexity.
- Database query efficiency.
- Connection pooling.
- Thread-pool configuration.
- Memory consumption.
- CPU utilization.
- Network overhead.
- Caching opportunities.
- Concurrency and synchronization.
- Horizontal scalability.

Avoid premature optimization. Identify actual bottlenecks before introducing complexity.

## 10. Security Guidelines

Security must be considered in every implementation.

- Never hardcode credentials or secrets.
- Never log passwords, access tokens or sensitive information.
- Follow existing authentication and authorization mechanisms.
- Validate user input.
- Use parameterized queries.
- Avoid exposing sensitive information through APIs.
- Follow least-privilege principles.
- Preserve existing Spring Security configurations.
- Do not weaken authentication or authorization to solve unrelated problems.

For OAuth2, OIDC, JWT and LDAP changes, understand the complete authentication and authorization flow before modifying code.

## 11. Logging and Exception Handling

- Use the existing logging framework.
- Use appropriate log levels.
- Include useful diagnostic context.
- Avoid excessive logging.
- Never log sensitive information.
- Preserve exception causes.
- Avoid catching exceptions without handling them meaningfully.
- Use consistent API error responses.
- Do not expose stack traces to external clients.

## 12. Testing Standards

Every meaningful feature should include appropriate tests.

Prefer:
- JUnit 5
- Mockito
- Spring Boot Test
- Integration tests
- Testcontainers where appropriate

Test:
- Happy paths.
- Error scenarios.
- Boundary conditions.
- Validation failures.
- Authorization failures.
- Concurrent operations where relevant.
- Retry and duplicate-processing scenarios for distributed workflows.

Do not remove existing tests simply to make a build pass.

## 13. Git and Code Management

- Do not overwrite existing user changes.
- Inspect Git status before broad modifications.
- Do not perform destructive Git operations without approval.
- Do not commit or push unless explicitly requested.
- Keep changes focused.
- Avoid unrelated formatting changes.
- Review the final diff before completing a task.

## 14. Dependency Management

- Prefer existing dependencies.
- Do not introduce libraries unnecessarily.
- Avoid changing framework versions without a clear reason.
- Explain why a new dependency is required.
- Follow the project's Maven or Gradle conventions.

## 15. Important Rules for Claude

1. Never assume the existing implementation without inspecting the code.
2. Never rewrite an entire class when a small modification is sufficient.
3. Never introduce architectural changes without explaining them.
4. Never remove existing functionality unless explicitly requested.
5. Never make assumptions about database schemas or API contracts.
6. Never ignore backward compatibility.
7. Never claim successful compilation or testing without verification.
8. Ask for clarification when a critical requirement is ambiguous.
9. Explain important technical trade-offs.
10. Prefer maintainable enterprise-grade solutions over quick hacks.

## 16. Expected Response Format

For substantial implementation tasks, provide:

### Understanding
Briefly explain the requirement and existing implementation.

### Proposed Approach
Explain the design and implementation strategy.

### Changes Made
List the important files modified and why.

### Technical Decisions
Explain important architectural decisions and trade-offs.

### Verification
List commands executed and test results.

### Risks
Mention compatibility, performance, security or operational concerns.

---

## 17. Project-Specific Instructions

Update this section with actual project details:

- Application architecture:
- Important modules:
- Database technologies:
- Kafka topics:
- Authentication mechanism:
- API conventions:
- Build commands:
- Test commands:
- Deployment approach:
- Known technical constraints:
