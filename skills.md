## Claude Code Skills

## What are Skills? Skills are reusable workflows and specialized instructions that teach Claude Code how to perform recurring tasks consistently.

Think of a Skill as a standard operating procedure (**SOP**) for Claude.

For example, suppose you frequently ask Claude to:
- Implement a Spring Boot **REST** **API**.
- Review Java code like a Principal Engineer.
- Debug a production issue.
- Design a Kafka-based distributed system.
- Generate unit and integration tests.
- Review database queries and performance.
Instead of writing detailed instructions every time, you create a Skill once and reuse it.

You can invoke a Skill manually using a slash command, or Claude can automatically identify when it is relevant. Claude Code Docs+1

## CLAUDE.md vs Rules vs Skills

These three concepts serve different purposes.
| Feature | What it contains | Example |
| --- | --- | --- |
| **CLAUDE**.md | Project context and general instructions | Java 21, Spring Boot 3 |
| Rules | Coding standards and constraints | Never use field injection |
| Skills | Repeatable procedures | Implement a **REST** **API** |
| Hooks | Automated actions | Run formatter after edits |

An easy way to remember:
- **CLAUDE**.md: What should Claude know?
- Rules: What should Claude follow?
- Skills: How should Claude perform a task?
- Hooks: What should happen automatically?
## Where do Skills live?
For a project-level Skill:
my-spring-boot-project/
│
├── **CLAUDE**.md
├── pom.xml
├── src/
│
└── .claude/
    └── skills/
    ├── implement-api/
    │   └── **SKILL**.md
    │
    ├── code-review/
    │   └── **SKILL**.md
    │
    ├── debug-application/
    │   └── **SKILL**.md
    │
    ├── write-tests/
    │   └── **SKILL**.md
    │
    └── system-design/
    └── **SKILL**.md

For personal Skills available across projects: ~/.claude/skills/

Project Skills can be shared with teammates by committing .claude/skills/ to Git. Claude Code Docs+1

## Anatomy of a Skill

Every Skill requires a **SKILL**.md file.

A Skill generally has two parts: ## YAML frontmatter – defines the Skill's name, description and invocation behaviour. ## Markdown body – contains the actual instructions and workflow. ## Example: name: code-review description: Reviews Java and Spring Boot code for bugs, security issues, performance and maintainability. ## Use when asked to review code or inspect a pull request. # Code Review Skill

Act as a Principal Software Engineer.

When reviewing code:

## Understand the existing implementation.

## Identify potential bugs. ## Check security vulnerabilities. ## Evaluate performance. ## Review exception handling. ## Check test coverage. ## Suggest improvements.

Prioritize findings by severity.

Do not modify code unless explicitly requested.

Here:
- name defines the Skill identity.
- description helps Claude decide when to use it.
- The Markdown body defines how Claude should execute the task.
The description is particularly important for automatic invocation. 
Claude Code Docs+1

## Practical example: Spring Boot API implementation Skill

Suppose you repeatedly ask Claude to implement **REST** APIs following your enterprise coding standards.

Create: .claude/skills/implement-api/**SKILL**.md

Content:

--- name: implement-api ## description: Implements production-ready REST APIs using Java and Spring Boot. Use when creating a new API, adding endpoints, or implementing backend features. # Spring Boot REST API Implementation

Act as a Senior Java Backend Engineer.

## Objective

Implement a production-ready **REST** **API** following the existing project architecture.

## Step 1: Explore

Before implementation:

- Inspect the project structure.
- Identify existing Controller, Service and Repository patterns.
- Understand existing **DTO** conventions.
- Inspect exception handling.
- Check security configuration.
- Review similar APIs already implemented.

## Step 2: Design

Identify:

- **API** endpoint and **HTTP** method.
- Request and response DTOs.
- Validation requirements.
- Service-layer responsibilities.
- Repository or database changes.
- Authentication and authorization requirements.
- Error scenarios.

Present the proposed design before implementing.

## Step 3: Implementation

Follow these conventions:

- Use constructor-based dependency injection.
- Keep business logic in service classes.
- Use DTOs for **API** contracts.
- Apply appropriate validation.
- Use centralized exception handling.
- Follow existing transaction boundaries.
- Avoid unnecessary dependencies.
- Maintain backward compatibility.

## Step 4: Testing

Create appropriate tests for:

- Successful requests.
- Invalid requests.
- Business validation failures.
- Database failures.
- Authorization failures.

Use JUnit 5 and Mockito.

## Step 5: Verification

- Compile the application.
- Execute relevant tests.
- Review the Git diff.
- Report any remaining risks.

## Expected Output

Provide:

## API design.

## Files created or modified. ## Implementation details. ## Test cases. ## Build and test results. ## Potential risks.

Now, whenever you need a new **API**, invoke: /implement-api

Claude will follow the defined procedure. ## Example: Principal Engineer Code Review Skill This is another Skill I strongly recommend for enterprise development.

File: .claude/skills/principal-review/**SKILL**.md

--- name: principal-review ## description: Performs Principal Engineer-level reviews of Java, Spring Boot and distributed systems code. Use for pull request reviews, architecture reviews and production readiness assessments. # Principal Engineer Code Review

Act as a Principal Software Engineer with expertise in Java, Spring Boot, microservices and distributed systems.

## Review Areas

### 1. Correctness

- Identify logical errors.
- Check boundary conditions.
- Identify race conditions.
- Check null handling.

### 2. Architecture

- Evaluate separation of concerns.
- Check **SOLID** principles.
- Identify unnecessary coupling.
- Check whether abstractions are justified.

### 3. Performance

- Identify inefficient database queries.
- Check unnecessary object creation.
- Identify blocking operations.
- Review concurrency and scalability.

### 4. Security

- Check authentication and authorization.
- Identify sensitive information exposure.
- Review input validation.
- Check insecure configurations.

### 5. Distributed Systems

- Check retry behavior.
- Check idempotency.
- Identify consistency problems.
- Review timeout handling.
- Check failure recovery.

### 6. Testing

- Evaluate unit test coverage.
- Identify missing edge cases.
- Check integration test requirements.

## Review Output

Categorize findings:

- **CRITICAL**: Security vulnerabilities or severe data-loss risks.
- **HIGH**: Significant correctness or reliability issues.
- **MEDIUM**: Performance or maintainability concerns.
- **LOW**: Minor improvements.

For every finding, provide:

## File and location.

## Problem description. ## Why it matters. ## Recommended correction.

Do not modify source code during a review. Do not report speculative issues as confirmed bugs. Prioritize actionable findings.

Invoke: /principal-review

## Example: Debugging Skill

File: .claude/skills/debug-application/**SKILL**.md

--- name: debug-application ## description: Systematically investigates Java and Spring Boot application errors, exceptions, performance problems and production incidents. Use when debugging application failures. # Application Debugging Workflow

Act as an experienced Java production support engineer.

## Step 1: Understand the Issue

Collect:
- Error message.
- Stack trace.
- Relevant source files.
- Application logs.
- Recent code changes.
- Environment details.

## Step 2: Trace the Execution Flow

- Identify the failing component.
- Trace the call hierarchy.
- Identify upstream and downstream dependencies.
- Inspect relevant configuration.
- Check database and network interactions.

## Step 3: Root Cause Analysis

Separate:
- Confirmed facts.
- Possible causes.
- Missing evidence.

Do not immediately propose a code change based only on the first exception.

## Step 4: Fix

- Recommend the smallest appropriate fix.
- Explain why it resolves the issue.
- Consider side effects.
- Preserve existing behavior.

## Step 5: Verification

- Reproduce the original failure if possible.
- Add regression tests.
- Execute relevant tests.
- Check for related failure scenarios.

## Final Report

Provide: ## Root cause. ## Evidence. ## Recommended fix. ## Files modified. ## Verification results.

Invoke: /debug-application

## Advanced Skill features

Skills support more than just reusable Markdown instructions.
Feature	Purpose
disable-model-invocation	Prevent Claude from automatically invoking the Skill
user-invocable	Control whether the Skill appears as a user-invocable slash command
allowed-tools	Pre-approve specified tools while the Skill is active
context: fork	Run the Skill in an isolated subagent context
agent	Select the subagent type for a forked Skill
paths	Restrict automatic activation to matching file paths
Supporting files	Provide scripts, templates and reference documentation

These options are defined in the Skill's **YAML** frontmatter. Claude Code Docs+1

Example A: A Skill that only you can invoke ## Consider a production deployment Skill. You don't want Claude deciding to deploy automatically just because you discussed deployment. name: deploy-production description: Deploys the application to production. ## disable-model-invocation: true You must explicitly invoke: /deploy-production

Example B: Run a Skill in a separate subagent ## Suppose you want an independent architecture review. name: architecture-review description: Reviews application architecture and identifies scalability and reliability risks. context: fork ## agent: Explore This runs the Skill in a separate subagent context. It does not inherit the full conversation history, so include all necessary task details in the Skill instructions or invocation. Claude Code Docs+1

Example C: Skill with supporting files
For a larger implementation workflow, keep the main Skill concise and organize reference material separately.
.claude/skills/implement-api/
│
├── **SKILL**.md
│
├── references/
│   ├── api-conventions.md
│   ├── exception-handling.md
│   └── testing-guidelines.md
│
├── templates/
│   └── controller-template.java
│
└── scripts/
    └── verify-build.sh

Claude can consult these supporting files when required instead of loading all the documentation into the main Skill instructions. resources.anthropic.com+1

## Skills I would create for your engineering workflow

I would start with these eight:
| Skill | Slash command | Purpose |
| --- | --- | --- |
| **API** implementation | /implement-api | Implement Spring Boot APIs |
| Code review | /principal-review | Principal Engineer review |
| Debugging | /debug-application | Root cause analysis |
| Unit testing | /write-tests | Generate JUnit and Mockito tests |
| System design | /system-design | Design scalable distributed systems |
| Database review | /database-review | Review schema and query performance |
| Kafka design | /kafka-design | Design producers, consumers and topics |
| Documentation | /document-api | Generate **API** and architecture documentation |

## Skills vs Subagents: an important distinction

These concepts are related but not interchangeable. Skill Knowledge and workflow

Provides Claude with instructions for performing a particular task.
    Example: How to conduct a Principal Engineer code review.

Subagent Independent worker

Performs a delegated task in its own context.
    Example: A security reviewer independently examines the authentication implementation.

A subagent can also preload Skills, and a Skill can run inside a subagent using context: fork. ## How to create and test your first Skill From your project root: mkdir -p .claude/skills/implement-api

touch .claude/skills/implement-api/**SKILL**.md

Paste the Spring Boot **API** Skill content above.

Start Claude Code: claude

Then invoke: /implement-api

You can also ask Claude to implement an **API** in natural language. If the Skill description matches, Claude may select it automatically.

To check available commands and Skills, use: /help

Recommended learning sequence
## CLAUDE.md
    
    Understand project-level context.
2. Rules
    
    Learn scoped and reusable coding instructions.
## Skills
    
    Create reusable development workflows.
## Subagents
    
    Delegate independent engineering tasks.
5. Hooks
    
    Automate deterministic checks and actions.
6. **MCP**
    
    Connect Claude to external tools and services.

My recommendation: Build the implement-api and principal-review Skills first. Use them on an actual Spring Boot feature. This will demonstrate the practical value of Skills much better than creating many theoretical examples.
