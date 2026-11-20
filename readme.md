# Claude Code – Developer Guide

## Introduction

Claude Code is an AI-powered coding agent that works directly with your codebase. You can use it to:

- Understand existing applications.
- Implement new features.
- Debug issues.
- Write unit and integration tests.
- Refactor code.
- Review pull requests.
- Execute development workflows.

For an experienced Java/Spring Boot and distributed-systems developer, the best approach is to treat Claude Code as a **development agent working under engineering constraints**, not simply as a code-generation chatbot.

---

## 1. Recommended Development Workflow

Follow this workflow for most development tasks:

1. **Explore:** Understand the architecture, dependencies and existing implementation.
2. **Plan:** Identify files, API contracts, database changes, risks and tests.
3. **Implement:** Make small, focused changes following existing conventions.
4. **Verify:** Compile, run tests and validate behaviour.
5. **Review:** Inspect the diff, security implications and backward compatibility.
6. **Commit:** Create a meaningful commit and prepare the PR.

The recommended approach is:

`Explore → Plan → Implement → Verify → Review`

---

## 2. Installation and Initial Setup

On macOS, install Claude Code using the native installer:

```bash
curl -fsSL https://claude.ai/install.sh | bash
```

Verify the installation:

```bash
claude --version
claude doctor
```

Navigate to your project:

```bash
cd my-spring-boot-project
claude
```

Initialize project instructions:

```text
/init
```

This helps Claude inspect the repository and create an initial `CLAUDE.md` file. Review and refine the generated file.

Official documentation:

- [Installation and Quickstart](https://code.claude.com/docs/en/quickstart)
- [Claude Code Overview](https://code.claude.com/docs/en/overview)

---

## 3. Important Commands and Shortcuts

### Slash commands

| Command | Purpose |
|---|---|
| `/init` | Initialize project instructions |
| `/help` | Show available commands |
| `/clear` | Start a fresh conversation |
| `/compact` | Compact conversation context |
| `/context` | Inspect context usage |
| `/memory` | Inspect memory |
| `/permissions` | Review permission configuration |
| `/doctor` | Diagnose installation |
| `/plan` | Planning workflow, where available |
| `/debug` | Debugging workflow, where available |
| `/code-review` | Code review workflow |
| `/rewind` | Restore an earlier checkpoint, where supported |

### Useful shortcuts

| Shortcut | Purpose |
|---|---|
| `Shift + Tab` | Cycle permission modes |
| `Esc` | Interrupt Claude |
| `@` | Reference a file or directory |
| `#` | Add information to project instructions |

**Note:** Commands and shortcuts can change across releases. Use `/help` to check your installed version.

---

## 4. CLAUDE.md – Project Configuration

Think of `CLAUDE.md` as the onboarding document you provide to a new developer joining your project.

It tells Claude:

- What the application does.
- How the project is structured.
- Which coding conventions to follow.
- How to build and test.
- What architectural constraints must be respected.

### Recommended directory structure

```text
my-project/
│
├── CLAUDE.md
├── pom.xml
├── src/
│   ├── main/
│   └── test/
│
└── .claude/
    ├── settings.json
    ├── rules/
    │   ├── java.md
    │   ├── testing.md
    │   └── security.md
    │
    ├── skills/
    │   ├── implement-feature/
    │   │   └── SKILL.md
    │   ├── debug-api/
    │   │   └── SKILL.md
    │   └── review-pr/
    │       └── SKILL.md
    │
    └── agents/
        ├── architecture-reviewer.md
        ├── security-reviewer.md
        └── test-engineer.md
```

### Example CLAUDE.md for Java Spring Boot

```markdown
# Project Overview

This is a Java Spring Boot enterprise application.

# Technology Stack

- Java 21
- Spring Boot 3.x
- Spring Security
- Spring Data JPA
- PostgreSQL
- Maven
- JUnit 5 and Mockito

# Architecture

- Follow layered architecture.
- Controllers handle HTTP requests and responses.
- Services contain business logic.
- Repositories handle database access.
- DTOs are used for API request and response models.
- Entities must not be exposed directly through REST APIs.

# Coding Standards

- Follow existing project conventions.
- Prefer constructor injection.
- Use meaningful names.
- Keep methods focused and small.
- Avoid unnecessary abstractions.
- Use Optional appropriately.
- Handle exceptions centrally.
- Do not introduce new dependencies without justification.

# Database

- Use existing database migration conventions.
- Never modify production data.
- Review schema changes for backward compatibility.
- Avoid N+1 query problems.

# Security

- Never hardcode credentials or secrets.
- Validate all external inputs.
- Enforce authorization at the appropriate layer.
- Do not log passwords, access tokens or sensitive data.

# Testing

- Write unit tests for business logic.
- Write integration tests for API and persistence changes.
- Run relevant tests after implementation.
- Do not modify tests merely to hide implementation failures.

# Development Workflow

- Understand existing implementation before changing code.
- Prepare a plan for non-trivial changes.
- Preserve existing API contracts unless explicitly requested.
- Explain important design decisions.
- Run Maven tests and build before declaring completion.
- Summarize files changed, tests executed and known limitations.
```

**Best practice:** Keep the root `CLAUDE.md` concise. Put detailed procedures into skills and specialized instructions into scoped rules.

---

## 5. Skills vs Rules vs Hooks vs Subagents

These are important Claude Code concepts.

| Feature | Main purpose | Example |
|---|---|---|
| CLAUDE.md | Persistent project context | Java 21, Spring Boot architecture |
| Rules | Scoped instructions | Java coding standards |
| Skills | Reusable workflows | Implement a REST API |
| Hooks | Deterministic automation | Run formatting or tests |
| Subagents | Delegated specialist work | Security review |

### CLAUDE.md – Persistent context

What should Claude know about the project?

Example:

- Java 21.
- Spring Boot architecture.
- Maven commands.
- Database conventions.

### Rules – Scoped instructions

What rules apply to particular files or areas?

Example:

- Controller classes must validate request DTOs.
- Repository classes must follow existing persistence conventions.

### Skills – Reusable workflows

How should Claude perform a repeatable task?

Example:

- Implement a REST API.
- Debug an API.
- Review a pull request.

### Hooks – Deterministic automation

What must happen automatically?

Example:

- Run formatting after file modifications.
- Execute mandatory checks.
- Prevent certain operations.

### Subagents – Specialist work

Who should independently investigate or review something?

Example:

- Architecture reviewer.
- Security reviewer.
- Test engineer.

Important distinction: CLAUDE.md and skills guide the model, but do not guarantee compliance. Hooks and permission-deny rules can enforce certain requirements deterministically.

---

## 6. Creating a Custom Skill

Suppose you repeatedly ask Claude to implement Spring Boot APIs.

Create:

`.claude/skills/implement-api/SKILL.md`

```markdown
---
name: implement-api
description: Implement REST APIs following existing Spring Boot project conventions.
---

# Implement REST API

1. Inspect existing controllers, services and repositories.
2. Understand existing API response and exception patterns.
3. Identify the request and response DTOs.
4. Propose an implementation plan.
5. Implement the controller and service changes.
6. Add validation and authorization.
7. Add unit and integration tests.
8. Run relevant Maven tests.
9. Review backward compatibility.
10. Summarize all changes and test results.

Do not introduce new dependencies without justification.
Do not change unrelated modules.
```

Invoke it using:

```text
/implement-api
```

Skills are particularly useful for repeatable development procedures.

Documentation: [Claude Code Skills](https://code.claude.com/docs/en/skills)

---

## 7. Permission Modes and Safety

Claude Code can read files, modify code and execute shell commands.

Understand the permission model before allowing autonomous work.

| Mode | Behaviour |
|---|---|
| Plan | Read-only planning |
| Default/manual approval | Requests permission for actions requiring approval |
| Accept edits | Automatically accepts file edits but still asks for other permissions |
| Auto | Uses automatic permission handling and safety checks where supported |

### Recommended usage

- Exploration: Plan mode.
- Feature implementation: Accept edits or manual approval.
- Production scripts: Manual approval.
- Database changes: Manual approval.
- Deployment operations: Manual approval.

Never blindly authorize destructive commands:

```bash
rm -rf
git push --force
DROP TABLE
kubectl delete
```

Use `/permissions` to review permissions and `.claude/settings.json` for project-level configuration.

---

## 8. Practical Development Scenarios

### Scenario A: Understand an Existing API

Instead of asking:

```text
Explain this API.
```

Use a more detailed prompt:

```text
Analyze the complete implementation of the customer API.

Trace the request from the REST controller to:
- Service layer
- Repository
- Database entities
- External service calls

Explain:

1. Request and response flow.
2. Business logic.
3. Database interactions.
4. Exception handling.
5. Security and authorization.
6. Dependencies on other services.

Do not modify any code.

Provide a sequence diagram and identify potential design issues.
```

### Scenario B: Implement a New Feature

Example: Implement a transaction reporting API.

```text
Implement a transaction reporting API.

Requirements:
- GET /api/v1/reports/transactions
- Support date range filtering.
- Support pagination and sorting.
- Return total transaction amount and count.
- Enforce existing authorization rules.

First inspect the existing codebase and identify reusable patterns.

Do not write code yet.

Prepare an implementation plan covering:

- API design
- DTOs
- Service and repository changes
- Database query strategy
- Validation
- Security
- Unit and integration tests
- Performance considerations

Wait for my approval before implementation.
```

After reviewing the plan:

```text
Implement the approved plan.

Follow existing coding conventions.
Add tests.
Run the relevant Maven tests.
Do not modify unrelated code.

Report all changed files and test results.
```

### Scenario C: Debug a Production Issue

```text
Investigate the following production issue.

Error:
Request header is too large.

Context:
Spring Boot backend receives requests from a Next.js frontend.

Please:

1. Trace the request path.
2. Identify where the header size is configured.
3. Inspect possible causes such as oversized JWTs or cookies.
4. Check whether the issue is in the proxy, frontend or backend.
5. Identify the root cause before proposing changes.

Do not modify code until the root cause is established.

Provide a fix and explain any security implications.
```

### Scenario D: Principal Engineer Code Review

```text
Review the current Git diff as a Principal Software Engineer.

Evaluate:

1. Architectural correctness.
2. SOLID principles and maintainability.
3. Thread safety and concurrency.
4. Performance and scalability.
5. Database query efficiency.
6. Security vulnerabilities.
7. Error handling.
8. Backward compatibility.
9. Test coverage.

Prioritize findings by severity.

For each finding provide:

- File and line number.
- Problem.
- Business or technical impact.
- Recommended fix.

Do not modify the code.
```

---

## 9. Subagents – Multiple Specialist Agents

For a large feature, one agent can implement while other agents independently inspect different aspects.

Example:

```text
Main Claude Code session
        |
        |-- Architecture reviewer
        |
        |-- Security reviewer
        |
        |-- Test engineer
        |
        |-- Performance reviewer
        |
        v
Consolidate findings
        |
        v
Fixes, tests and final review
```

Useful specialist agents:

- Architecture reviewer.
- Security reviewer.
- Performance reviewer.
- Database/query reviewer.
- Test engineer.

Subagents have their own context, making them useful for independent investigation and parallel review.

Avoid asking several agents to edit the same files simultaneously unless you have explicitly isolated their work.

---

## 10. Advanced Features

| Feature | Why it matters |
|---|---|
| Hooks | Automate checks and enforce workflow |
| MCP | Connect databases, GitHub, Jira and external tools |
| Skills | Reuse engineering procedures |
| Subagents | Parallelize investigations |
| Worktrees | Isolate parallel coding tasks |
| Headless mode (`-p`) | Automate Claude in scripts and CI |
| Auto memory | Retain useful project learnings |
| Checkpointing | Recover earlier work |

### Headless mode example

```bash
claude -p "Analyze the codebase and identify potential SQL injection vulnerabilities."
```

This can be used for automated analysis in CI, although security findings still require validation.

---

## 11. Recommended Learning Roadmap

### Level 1 – Core Usage

- Installation and CLI.
- File exploration.
- Prompt engineering.
- Plan mode.
- Permission management.

### Level 2 – Project Engineering

- CLAUDE.md.
- Rules.
- Feature implementation.
- Debugging.
- Testing and code review.

### Level 3 – Customization

- Skills.
- Hooks.
- MCP integration.
- Persistent project context.

### Level 4 – Agentic Development

- Subagents.
- Worktrees.
- Parallel development.
- Headless execution.
- CI/CD integration.

Recommended order: Master Levels 1 and 2 before moving into advanced agentic workflows.

---

## 12. Official Documentation and Learning Resources

1. [Claude Code Overview](https://code.claude.com/docs/en/overview)
2. [Installation and Quickstart](https://code.claude.com/docs/en/quickstart)
3. [Claude Code Best Practices](https://code.claude.com/docs/en/best-practices)
4. [Skills Documentation](https://code.claude.com/docs/en/skills)
5. [Claude Code in Action – Anthropic Course](https://anthropic.skilljar.com/claude-code-in-action)

---

## 13. Practical Project to Master Claude Code

Use an existing Spring Boot application rather than starting with a toy project.

### Exercise: Implement an End-to-End Feature

1. Ask Claude to understand the existing authentication and authorization flow.
2. Ask it to trace an existing REST API end to end.
3. Ask it to plan a new API.
4. Review and approve its plan.
5. Let it implement the API and tests.
6. Ask a separate agent to review the implementation.
7. Add a custom skill for the same workflow.
8. Add hooks for mandatory formatting and tests.

You'll learn more from this exercise than from memorizing dozens of commands.

---

## Final Recommendations

For enterprise development, remember these principles:

- Give Claude sufficient project context.
- Ask it to investigate before modifying code.
- Use Plan mode for non-trivial changes.
- Keep changes small and focused.
- Require tests and build verification.
- Review security and backward compatibility.
- Use skills for repeatable workflows.
- Use hooks for deterministic checks.
- Use subagents for independent specialist reviews.
- Keep control over production-impacting operations.

**The key principle:** Claude Code can generate code quickly, but the developer remains responsible for architectural decisions, security, correctness and production readiness.

Start with core usage and project configuration, then gradually introduce skills, hooks and subagents.
