Claude Code Subagents – Complete Guide with Examples
1. What are Subagents?
Subagents are specialized AI assistants that Claude Code can delegate tasks to.

A subagent works on a specific task using its own context, instructions, and tool permissions. It can investigate a codebase, review security, analyze performance, or perform other delegated work.

Think of Claude Code as a Technical Lead managing a team of specialized engineers.

For example, suppose you ask:
Review my Spring Boot application for security, performance and architectural issues.

Claude can delegate the work to:
- Security Reviewer
- Performance Engineer
- Architecture Reviewer
- Test Engineer
Each subagent independently investigates its assigned area and returns its findings to the main Claude session.
Main Claude – Technical Lead

Understands the request and delegates tasks


Security Reviewer

Authentication, authorization, vulnerabilities


Performance Engineer

Queries, memory, CPU, scalability


Architecture Reviewer

Coupling, design, resilience


Test Engineer

Coverage, edge cases, integration tests




Main Claude consolidates findings

Produces one consolidated review report


This is particularly useful for large enterprise applications where a single review involves several different areas of expertise.
2. Subagents vs Skills vs Rules
These concepts are related, but serve different purposes.
Feature	Purpose	Example
CLAUDE.md	Project context	Spring Boot 3, Java 21
Rules	Standards and constraints	Never use field injection
Skills	Reusable workflows	Implement REST API
Subagents	Independent delegated work	Security code reviewer
Hooks	Automated actions	Run tests after changes

The simplest distinction:
- Rule: What standards should be followed?
- Skill: How should a task be performed?
- Subagent: Who should perform an independent task?
- Hook: What should execute automatically?
3. Built-in subagents
Claude Code provides built-in subagents for common tasks. The available built-in types can vary by version.
Subagent	Responsibility
Explore	Quickly explores and understands a codebase
Plan	Researches implementation approaches and creates plans
General-purpose	Performs broader delegated tasks
Bash	Executes shell-oriented tasks
Other specialized types	May be available depending on the installed version

For example, Claude may use Explore to understand an existing Spring Security implementation before proposing changes.

You can also explicitly request delegation:
Use an Explore subagent to understand the
complete authentication flow in this project.


4. Creating your own custom subagent
Custom subagents are defined as Markdown files with YAML frontmatter.

Project-level location:
my-spring-boot-project/
│
├── CLAUDE.md
│
└── .claude/
    └── agents/
        ├── security-reviewer.md
        ├── performance-reviewer.md
        ├── architecture-reviewer.md
        └── test-engineer.md


Each Markdown file defines one specialist.
5. Example 1: Create a Security Reviewer subagent
This is particularly useful for your Spring Security, OAuth2, JWT and LDAP projects.

File:
.claude/agents/security-reviewer.md



---
name: security-reviewer
description: Reviews Java and Spring Boot applications for security vulnerabilities, especially Spring Security, OAuth2, OIDC, JWT and LDAP. Use proactively after authentication or authorization changes.
tools: Read, Grep, Glob
model: sonnet
---

# Security Reviewer

Act as a Senior Application Security Engineer.

Your responsibility is to review application code for security issues.

## Review Areas

### Authentication
- Review authentication provider configuration.
- Check password handling.
- Identify authentication bypass possibilities.
- Review LDAP and Active Directory integration.

### Authorization
- Review endpoint access restrictions.
- Check role and permission validation.
- Identify privilege escalation risks.

### OAuth2 and OIDC
- Validate authorization and token flows.
- Review redirect URI validation.
- Check client authentication.
- Review token expiration and refresh handling.

### JWT
- Check signature validation.
- Check expiration, issuer and audience validation.
- Review signing key management.

### General Security
- Identify hardcoded credentials.
- Check sensitive information in logs.
- Review input validation.
- Identify insecure configurations.

## Output Format

Categorize findings:

- CRITICAL
- HIGH
- MEDIUM
- LOW

For each finding provide:
1. File and location.
2. Description.
3. Security impact.
4. Recommended fix.

Do not modify source code.

Only report issues supported by code evidence.



Notice that this subagent has only Read, Grep and Glob tools. It can inspect your code but cannot edit files or execute shell commands.

This is an example of restricting a subagent to its actual responsibility.
6. Example 2: Performance Reviewer subagent
File:
.claude/agents/performance-reviewer.md



---
name: performance-reviewer
description: Reviews Java, Spring Boot, database and distributed systems code for performance bottlenecks, memory problems, concurrency issues and scalability risks. Use proactively when reviewing performance-sensitive changes.
tools: Read, Grep, Glob
model: sonnet
---

# Performance Reviewer

Act as a Principal Engineer specializing in high-performance distributed systems.

## Review Areas

### Java
- Identify inefficient algorithms.
- Review time and space complexity.
- Check unnecessary object creation.
- Identify memory leaks.
- Review thread safety.

### Spring Boot
- Review connection pool configuration.
- Identify blocking operations.
- Check thread pool utilization.
- Review caching opportunities.

### Database
- Identify N+1 queries.
- Review query patterns and indexes.
- Check unnecessary full scans.
- Identify inefficient pagination.

### Kafka
- Review partition strategy.
- Check consumer throughput.
- Identify potential consumer lag.
- Review batching and concurrency.

### Distributed Systems
- Identify network bottlenecks.
- Review timeout and retry behavior.
- Check hot partitions and hot keys.
- Evaluate horizontal scalability.

## Output

For each finding provide:

1. Component.
2. Potential bottleneck.
3. Performance impact.
4. Recommended improvement.
5. Priority.

Do not modify source code.

Avoid speculative performance claims.
Distinguish measured problems from potential risks.



7. Example 3: Test Engineer subagent
Unlike the previous two, this subagent may need permission to edit test files.

File:
.claude/agents/test-engineer.md



---
name: test-engineer
description: Creates and improves JUnit 5, Mockito and Spring Boot tests. Use when implementing features, improving test coverage or investigating test failures.
tools: Read, Grep, Glob, Edit, Write, Bash
model: sonnet
---

# Test Engineer

Act as a Senior Java Test Automation Engineer.

## Responsibilities

- Understand the implementation before writing tests.
- Identify important business scenarios.
- Create unit tests using JUnit 5.
- Use Mockito for dependency mocking.
- Create Spring Boot integration tests where appropriate.
- Use Testcontainers for infrastructure-dependent tests when appropriate.

## Test Scenarios

Always consider:

- Happy path.
- Invalid input.
- Null and empty values.
- Boundary conditions.
- Exception handling.
- Authorization failures.
- Concurrent operations.
- Duplicate message processing.

## Guidelines

- Follow existing test conventions.
- Avoid unnecessary mocking.
- Do not change production code merely to make tests pass.
- Do not delete failing tests without justification.
- Execute the relevant test suite.

## Final Report

Provide:
1. Test files created or modified.
2. Scenarios covered.
3. Test execution results.
4. Remaining coverage gaps.



The Bash tool allows this agent to execute build and test commands, subject to the session's permission controls. Use this additional access only when necessary.
8. How to invoke subagents
You can ask Claude to delegate work explicitly.

Example 1 – Security review
Use the security-reviewer subagent to review
the complete Spring Security implementation.

Focus on OAuth2, JWT and LDAP.
Do not modify any files.


Example 2 – Performance review
Use the performance-reviewer subagent to analyze
the Kafka consumer implementation.

Identify throughput bottlenecks and scalability risks.


Example 3 – Test generation
Use the test-engineer subagent to create unit tests
for CustomerServiceImpl.

Cover happy paths, exceptions and boundary conditions.


Claude can also select a custom subagent automatically when the request matches its description. Including specific use cases and the word proactively in the description can encourage delegation.
9. Parallel execution: Multiple subagents
This is where subagents become especially powerful.

Suppose you have implemented a payment processing microservice and want a comprehensive review.

You can ask:
Review the payment microservice.

Delegate independent tasks to:
1. security-reviewer
2. performance-reviewer
3. architecture-reviewer
4. test-engineer

Ask each agent to analyze the relevant code independently.

Do not allow any agent to modify production code.

Consolidate all findings into a single report.
Identify duplicate findings and prioritize issues.


Example: Parallel code review
Main Claude

Delegates four independent reviews

Security

OAuth2, JWT, access control


Performance

Latency, throughput, memory


Architecture

Coupling, resilience, design


Testing

Coverage, edge cases




Consolidated engineering review

Main Claude combines findings and recommendations.


The main Claude session can coordinate multiple independent subagents. Each runs in its own context window and returns its final result to the parent. This helps prevent large code searches and logs from consuming the main conversation's context. 
Claude Code Docs+1

10. Important subagent configuration options
Field	Purpose	Example
name	Unique agent identifier	security-reviewer
description	Explains when to use it	Security review
tools	Allowed tools	Read, Grep, Glob
disallowedTools	Explicitly excluded tools	Write, Edit
model	Model selection	sonnet, haiku, opus, inherit
permissionMode	Permission behaviour	plan, default
maxTurns	Maximum agent turns	20
memory	Persistent agent memory	project
skills	Skills to preload	principal-review
background	Prefer background execution	true
isolation	Isolate work in a Git worktree	worktree

You do not need to configure all these fields. Start with name, description, tools and model. 
Claude Code Docs+1

11. Subagents vs Agent Teams
This distinction is important.

Claude Code also supports a more collaborative feature called Agent Teams.
Aspect	Subagents	Agent Teams
Context	Independent context	Independent teammate contexts
Communication	Return results to parent	Teammates can communicate
Coordination	Main Claude delegates	Shared team coordination
Shared task list	No shared team task list	Shared task list
Best for	Independent investigation	Collaborative multi-agent work
Complexity	Lower	Higher

For example:
- Subagents: Four independent reviewers inspect a payment service and return findings.
- Agent Teams: Multiple agents collaborate on implementing different parts of a large feature, coordinating dependencies and progress.
Agent Teams are a separate, more involved mechanism. They consume additional resources and are not necessary for most code reviews. 
SFEIR Institute+1

12. Recommended subagent structure for your projects
I would start with these five specialists:
my-spring-boot-project/
│
├── CLAUDE.md
│
└── .claude/
    ├── rules/
    │   ├── java-coding.md
    │   ├── spring-boot.md
    │   ├── spring-security.md
    │   └── database.md
    │
    ├── skills/
    │   ├── implement-api/
    │   │   └── SKILL.md
    │   └── principal-review/
    │       └── SKILL.md
    │
    └── agents/
        ├── security-reviewer.md
        ├── performance-reviewer.md
        ├── architecture-reviewer.md
        ├── test-engineer.md
        └── debugging-specialist.md


13. Practical considerations
A few important points:
1. Context isolation: A subagent gets its own context window. It does not automatically inherit the complete conversation history of the main agent. Include important requirements in the delegation prompt.
2. Cost: Each subagent makes its own model requests, so parallel delegation can increase usage.
3. File changes: Avoid having multiple agents edit the same files concurrently. Use read-only reviewers where possible.
4. Permissions: Subagents have their own tool configuration, but the parent session's permission settings and organization policies can affect what they can do.
5. Nested delegation: Do not design your workflow assuming that subagents can recursively spawn more subagents.
6. Loading: Custom agent definitions are normally loaded when the session starts. If you create them directly on disk during an existing session, restart the session to load them.
14. My recommended learning exercise
Try this on an existing Spring Boot microservice:
Hands-on exercise
Build a multi-agent code review system
1. Create security-reviewer.md.
2. Create performance-reviewer.md.
3. Create architecture-reviewer.md.
4. Ask Claude to run all three reviews independently.
5. Compare the findings.
6. Ask the main Claude session to produce a prioritized remediation plan.
