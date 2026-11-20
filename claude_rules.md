## Claude Code Rules

In Claude Code, Rules are reusable project instructions that help Claude follow specific coding standards for particular parts of your codebase.

They are especially useful when a single **CLAUDE**.md becomes too large or when different modules need different instructions.

For example, in a Spring Boot project:
- Java coding rules apply to Java source files.
- Spring Security rules apply to authentication and authorization code.
- Database rules apply to repository and persistence code.
- Kafka rules apply to event-driven components.
The key difference is:
Feature	Purpose
**CLAUDE**.md	General project instructions
Rules	Specific instructions, optionally scoped to files
Skills	Reusable workflows or procedures
Hooks	Automated actions triggered by events

## Where do Rules live?

Create a .claude/rules/ directory in your project.
my-spring-boot-project/
├── **CLAUDE**.md
├── pom.xml
├── src/
│   └── main/
│       └── java/
└── .claude/
    └── rules/
    ├── java-coding.md
    ├── spring-boot.md
    ├── spring-security.md
    ├── database.md
    └── kafka.md

Claude Code automatically loads rule files from this directory. Rules without path restrictions apply broadly; rules with path frontmatter apply to matching files. ## Example: Java coding rules File: .claude/rules/java-coding.md # Java Coding Standards

Apply these rules to all Java source files.

## General

- Follow the Java version configured in pom.xml.
- Prefer clean, readable and maintainable code.
- Follow **SOLID** principles where appropriate.
- Prefer composition over inheritance.
- Avoid unnecessary abstractions and premature optimization.

## Dependency Injection

- Prefer constructor-based dependency injection.
- Avoid field injection using @Autowired.

## Exception Handling

- Never swallow exceptions.
- Preserve the original exception cause.
- Use meaningful custom exceptions where appropriate.
- Do not expose internal exception details to **API** consumers.

## Collections and Streams

- Use Java Streams when they improve readability.
- Avoid unnecessarily complex stream pipelines.
- Consider memory consumption when processing large collections.

## Performance

- Avoid unnecessary object creation.
- Consider time and space complexity.
- Avoid repeated database or network calls inside loops.

## Example: Spring Boot rules

File: .claude/rules/spring-boot.md # Spring Boot Development Rules

Apply these rules to Spring Boot application code.

## Architecture

- Keep Controller, Service and Repository responsibilities separate.
- Controllers should not contain business logic.
- Keep business logic in service classes.
- Use DTOs for **API** request and response models.

## REST APIs

- Follow **REST** conventions.
- Use appropriate **HTTP** status codes.
- Validate request payloads using Jakarta Bean Validation.
- Maintain backward compatibility.
- Use consistent **API** response structures.

## Transactions

- Apply @Transactional at the appropriate service boundary.
- Avoid unnecessarily long-running transactions.
- Understand transaction propagation before changing existing behavior.

## Configuration

- Externalize configuration.
- Use application.yml or application.properties.
- Avoid hardcoded URLs, credentials and environment-specific values.

## Testing

- Write unit tests for business logic.
- Use @WebMvcTest for controller tests where appropriate.
- Use @SpringBootTest for integration tests when necessary.

## Example: Spring Security rules

This is particularly useful for projects involving OAuth2, **OIDC**, **JWT** and **LDAP**.

## File: .claude/rules/spring-security.md

paths:
    - *src/main/java/**/*Security*.java*
    - *src/main/java/**/*Auth*.java*
    - *src/main/java/**/*Jwt*.java*
##   - *src/main/java/**/*Ldap*.java*
# Spring Security Rules

These rules apply to authentication and authorization components.

## Authentication

- Understand the complete authentication flow before making changes.
- Preserve existing authentication providers.
- Do not bypass authentication to fix unrelated issues.
- Keep authentication and authorization responsibilities separate.

## OAuth2 and OIDC

- Follow OAuth2 and **OIDC** specifications.
- Preserve existing authorization server configuration.
- Validate redirect URIs.
- Handle authorization codes and tokens securely.
- Never log access tokens, refresh tokens or client secrets.

## JWT

- Validate token signature and expiration.
- Validate issuer and audience where applicable.
- Follow existing signing algorithms and key-management practices.
- Never hardcode signing keys.

## LDAP / Active Directory

- Preserve existing **LDAP** provider behavior.
- Consider connection timeouts and directory unavailability.
- Handle authentication failures separately from infrastructure failures.
- Do not silently fall back to another authentication provider without an explicit policy.

## Testing

- Test successful authentication.
- Test invalid credentials.
- Test expired tokens.
- Test insufficient permissions.
- Test **LDAP** timeout and fallback scenarios.

Notice the **YAML** paths section at the beginning. It tells Claude when this rule is relevant. It avoids loading security-specific instructions for unrelated tasks. Claude Code Docs+1

## Example: Database rules

## File: .claude/rules/database.md
paths:
    - *src/main/java/**/*Repository.java*
    - *src/main/java/**/*Entity.java*
    - *src/main/java/**/*Document.java*
    - *src/main/java/**/*RepositoryImpl.java*
##   - *src/main/resources/db/migration/***
# Database Development Rules

## General

- Understand existing schema before changing persistence code.
- Avoid unnecessary database calls.
- Consider transaction boundaries.
- Avoid N+1 query problems.
- Use pagination for large result sets.
- Review indexes before optimizing queries.

## JPA / Hibernate

- Avoid unintentional eager loading.
- Understand cascade operations.
- Avoid bidirectional relationship complexity unless necessary.
- Use **DTO** projections for read-heavy queries where appropriate.

## MongoDB

- Design documents based on access patterns.
- Avoid unbounded arrays.
- Consider compound indexes.
- Avoid loading unnecessary documents.

## Cassandra

- Design tables based on query patterns.
- Choose partition keys carefully.
- Avoid large partitions.
- Use clustering columns to support required query patterns.
- Avoid unrestricted **ALLOW** **FILTERING**.
- Consider consistency levels and replication.

## Database Migrations

- Never modify an already-applied production migration.
- Create a new migration for schema changes.
- Consider backward compatibility.
- Never execute destructive migrations without approval.

## Example: Kafka rules

## File: .claude/rules/kafka.md
paths:
    - *src/main/java/**/*Kafka*.java*
    - *src/main/java/**/*Consumer*.java*
    - *src/main/java/**/*Producer*.java*
##   - *src/main/java/**/*Listener*.java*
# Kafka Development Rules

## Producer

- Choose partition keys based on ordering requirements.
- Configure acknowledgements appropriately.
- Consider retries and duplicate message delivery.
- Use schema compatibility practices.

## Consumer

- Design consumers to be idempotent.
- Handle duplicate messages.
- Understand offset commit behavior.
- Handle consumer rebalancing.
- Consider retry and dead-letter strategies.

## Reliability

- Do not assume exactly-once processing across external systems.
- Consider consumer lag and throughput.
- Handle poison messages.
- Avoid blocking consumer threads with long-running operations.

## Testing

- Test duplicate message handling.
- Test retry behavior.
- Test invalid messages.
- Test consumer recovery scenarios.

## Global personal rules

You can also create rules that apply to all your projects, not just one repository.

Location: ~/.claude/rules/

Example: ~/.claude/rules/development-preferences.md # My Development Preferences

- Prefer practical enterprise-grade solutions.
- Explain architectural decisions and trade-offs.
- Do not overengineer simple requirements.
- Explore existing code before suggesting modifications.
- Prefer minimal changes over rewriting entire modules.
- Use Java examples where appropriate.
- Consider scalability, reliability and maintainability.
- Include edge cases in implementation discussions.
- Explain why a particular approach is recommended.
- Never claim successful execution without verification.

This is useful because these preferences follow you across projects. Claude Code Docs+1

## How Rules, CLAUDE.md and Skills work together

Consider this practical example:

You ask Claude: Implement a new OAuth2 login provider using Spring Security and **LDAP** fallback.

Claude can use the instructions as follows: Step 1 **CLAUDE**.md

Understands the overall architecture, Java version, project conventions and development workflow.

Step 2 spring-boot.md

Applies Spring Boot architecture and service-layer conventions.

Step 3 spring-security.md

Applies OAuth2, **OIDC**, **JWT** and **LDAP**-specific security instructions when matching files are accessed.

Step 4 Skill: implement-authentication

Can provide a reusable step-by-step implementation workflow, including design, coding and testing.

Step 5 Hooks and permissions

Can enforce or restrict certain actions, such as preventing unauthorized modifications to sensitive files.

## How to verify whether Rules are working

Start Claude Code in your project: cd my-spring-boot-project claude

Then try these prompts:

Prompt 1 – General instructions Explain the project architecture and summarize the coding conventions you must follow.

Prompt 2 – Security rules Review the existing Spring Security configuration. Explain which security-specific development rules apply to these files.

Prompt 3 – Database rules Review the Cassandra repository implementation. Identify the database rules that apply.

You can also inspect the loaded context using: /context

My recommendation for your project
Start with these five rules rather than creating too many files immediately:
Rule	Scope
java-coding.md	Java code
spring-boot.md	Spring Boot
spring-security.md	Authentication and authorization
database.md	Persistence layer
kafka.md	Messaging

One important distinction: Rules are guidance, not hard enforcement. For mandatory restrictions, use permissions, hooks, tests and linters.
