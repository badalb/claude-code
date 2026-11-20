This guide covers Claude Code from a developer, senior engineer and AI-assisted software engineering perspective. It focuses not only on definitions, but also on architecture, internals, practical usage, trade-offs and scenario-based interview questions.

A useful way to prepare is to understand five areas:
## Claude Code fundamentals and architecture
## Context management and session lifecycle
## Customization: CLAUDE.md, Rules, Skills and Hooks
## Agents, subagents and MCP
## Enterprise usage, security and real-world development workflows
## Core concepts you must understand
Topic	What an interviewer may expect
Claude Code	Agentic coding tool that understands and modifies codebases
Agentic workflow	Explore, plan, execute, verify and iterate
Context window	How conversation, files, tool results and instructions consume context
Session management	Continuing, clearing, resuming and managing work across sessions
**CLAUDE**.md	Persistent project-level instructions and context
Rules	Reusable coding standards and constraints
Skills	Reusable task-specific workflows
Hooks	Automated actions triggered by lifecycle events
Subagents	Independent agents with their own context
Agent Teams	Multiple collaborating agents
**MCP**	Standardized integration with external tools and systems
Permission modes	Controls over tool execution and approval
Checkpoints	Recovering from unwanted changes
Headless mode	Running Claude Code non-interactively
Enterprise security	Permissions, secrets, prompt injection and governance

## Frequently asked questions

- Fundamentals and architecture
Q1. What is Claude Code?

Claude Code is an agentic coding assistant that works with a developer's codebase. It can:
- Read and understand source code.
- Search files and trace dependencies.
- Plan and implement features.
- Execute shell commands and tests.
- Debug errors.
- Review code.
- Interact with external systems through **MCP**.
The important distinction is that it is not simply a code-completion tool. It can execute a multi-step development workflow using tools, subject to configured permissions.

Q2. How does Claude Code work internally?

A useful conceptual architecture: Developer

Natural-language task

### Claude Code Agent Runtime

Context · Instructions · Planning · Tool orchestration

Read / Search

Codebase exploration

Edit / Write

Code changes

Bash

Builds and tests

**MCP** / Agents

External tools and delegation

Results and feedback

Tool output · Test results · Updated context

The agent repeatedly evaluates results and decides what to do next. This is commonly called a tool-use or agent loop.

Q3. What is the difference between Claude Code and GitHub Copilot?
| Area | Claude Code | Traditional Copilot coding workflow |
| --- | --- | --- |
| Primary interaction | Agentic task execution | Code suggestions and assistance |
| Codebase exploration | Multi-step exploration | Depends on product and mode |
| File modifications | Can modify multiple files | Often developer-directed |
| Terminal | Can execute commands | Depends on integration |
| Task delegation | Subagents | Varies by product |
| Automation | Hooks, skills, **MCP** | Varies by product |

A good interview answer should acknowledge that modern coding assistants increasingly overlap in these capabilities.
- Context and session management
Q4. What is a context window?

The context window is the amount of information a model can consider in a particular interaction.

It can include:
- System instructions.
- Conversation history.
- **CLAUDE**.md and other loaded instructions.
- Source code and file contents.
- Tool results.
- Intermediate reasoning context.
A large codebase does not automatically mean the entire codebase is loaded into the context window.

Q5. What happens when context becomes full?

Claude Code can compact earlier conversation context into a shorter summary, preserving important information for continued work.

However, compaction can lose implementation details if they were not captured properly.

Good practices:
- Keep **CLAUDE**.md focused.
- Save important architectural decisions.
- Use /compact when appropriate.
- Start a fresh session for unrelated tasks.
- Maintain progress notes for lengthy implementations.
Q6. What is the difference between /clear, /compact and /resume?
Command	Purpose
/clear	Starts a clean conversation context
/compact	Summarizes and compresses existing context
/resume	Reopens a previous session
/context	Inspects context usage
/rewind	Returns to an earlier checkpoint

Command availability and behavior can vary with Claude Code versions.

Q7. How would you manage a multi-day feature implementation?

A strong answer:
## Create a feature branch.
## Ask Claude to explore the existing architecture.
## Generate an implementation plan.
## Save the plan and important decisions.
## Implement one logical unit at a time.
## Run tests after each meaningful change.
## Commit stable milestones.
## Resume the session or start a new one with a concise progress summary.
- **CLAUDE**.md, Rules and Skills
Q8. What is **CLAUDE**.md?

It is a Markdown instruction file that provides persistent project context and development guidance.

Example: # Project Guidelines

## Technology

- Java 21
- Spring Boot 3
- Maven
- PostgreSQL
- Kafka

## Architecture

- Follow layered architecture.
- Keep business logic in service classes.
- Use DTOs for **API** boundaries.

## Coding Standards

- Prefer constructor injection.
- Use meaningful exception handling.
- Avoid unnecessary dependencies.

## Testing

- Use JUnit 5 and Mockito.
- Add integration tests for critical flows.
- Run ./mvnw test before completion.

Q9. Explain the difference between **CLAUDE**.md, Rules and Skills.
| Feature | Purpose | Example |
| --- | --- | --- |
| **CLAUDE**.md | Project-wide context | Build commands, architecture |
| Rules | Coding standards and constraints | Spring Security conventions |
| Skills | Reusable procedures | Implement a **REST** **API** |
| Hooks | Automated enforcement/actions | Run formatter after editing |
| Subagents | Delegate independent work | Security review |

A useful way to remember:
- Rules say what standards to follow.
- Skills say how to perform a task.
- Hooks make specified actions happen automatically.
- Subagents perform delegated work independently.
**Q10**. How would you create a reusable **API** implementation Skill?

Example structure:
.claude/
    skills/
    implement-api/
    **SKILL**.md

## Example SKILL.md:

name: implement-api ## description: Implement a REST API using existing project conventions. # API Implementation Workflow

## Explore existing controllers and services.

## Identify the appropriate domain model. ## Design request and response DTOs. ## Implement controller and service layers. ## Add validation and exception handling. ## Add unit and integration tests. ## Run the relevant test suite. ## Summarize files changed and design decisions.

Skills are particularly useful when a workflow is repeated across multiple projects. Claude by Anthropic+1

- Hooks and permissions
**Q11**. What are Claude Code Hooks?

Hooks execute configured actions at specific lifecycle events.

Examples:
- PreToolUse: inspect or block a tool invocation.
- PostToolUse: format files after an edit.
- SessionStart: load project information.
- Stop: perform configured checks when a response finishes.
- PreCompact: preserve information before compaction.
**Q12**. Why use Hooks instead of **CLAUDE**.md?

Suppose you want every Java file to be formatted after modification.

A **CLAUDE**.md instruction merely asks Claude to format the file. A configured PostToolUse hook can invoke the formatter automatically after a matching edit event.

Hooks are useful for deterministic automation and guardrails. Claude by Anthropic+1

**Q13**. Explain Claude Code permission modes.
Mode	Purpose
Default / Manual	Ask before actions requiring approval
Accept Edits	Allow file edits while retaining other approvals
Plan	Read-only planning
Auto	Background safety evaluation and approval automation
Don't Ask	Use pre-approved actions; deny other requests
Bypass Permissions	Bypass normal permission checks

**Q14**. How would you protect an enterprise codebase?
- Restrict destructive shell commands.
- Avoid storing credentials in prompts or project files.
- Use scoped permissions.
- Protect production deployment operations.
- Review **MCP** server permissions.
- Use hooks for deterministic restrictions.
- Require human approval for sensitive operations.
- Keep an audit trail of important changes.
- Subagents, Agent Teams and **MCP**
**Q15**. What are Subagents?

Subagents are specialized agents that perform delegated tasks using their own context windows.

For example, for a Spring Boot authentication change, you could delegate:
- Security review.
- Performance review.
- Test generation.
- Architecture review.
The main agent collects and consolidates their findings.

**Q16**. What is the difference between Skills and Subagents?
Skills	Subagents
Reusable knowledge/workflow	Independent delegated agent
Usually execute within the current workflow	Have separate context
Define how to perform a task	Perform a delegated task
Example: **API** implementation procedure	Example: Security reviewer

**Q17**. What is the difference between Subagents and Agent Teams?

Subagents are appropriate for independent delegated tasks where the parent agent needs the results.

Agent Teams are designed for more collaborative multi-agent work, including communication and coordination between teammates. They can add complexity and resource overhead.

**Q18**. What is **MCP**?

Model Context Protocol is a standardized way to connect AI applications to external tools and data sources.

For example:
### Claude Code
    |
    | MCP
    |
    +---- GitHub
    |
    +---- Jira
    |
    +---- PostgreSQL
    |
    +---- Internal APIs
    |
    +---- Monitoring Platform

**MCP** server capabilities commonly include:
- Tools: execute actions.
- Resources: expose contextual data.
- Prompts: provide reusable prompt templates.
**Q19**. How would you integrate Claude Code with Jira and GitHub?

A practical design: ## Configure authorized MCP servers. ## Authenticate using appropriately scoped credentials. ## Allow Claude to retrieve Jira requirements. ## Explore the relevant GitHub repository. ## Implement the requested feature. ## Run tests and review changes. ## Create a pull request only after the required approval. **MCP** provides integration; it does not automatically make every external action safe. Claude Help Center+1

## Scenario-based interview questions

These are particularly important for Senior Engineer and Principal Engineer interviews.

Scenario 1: Implement a feature across a large Spring Boot codebase.

Question: You need to implement a new payment **API** involving controller, service, repository, Kafka producer and database changes. How would you use Claude Code?

Expected approach: ## Start in Plan mode. ## Ask Claude to trace the existing payment flow. ## Identify API contracts, domain entities and dependencies. ## Prepare a change plan. ## Implement incrementally. ## Delegate independent reviews to subagents. ## Run unit and integration tests. ## Inspect the Git diff. ## Commit only after verification. Scenario 2: Claude modifies unrelated files. How would you prevent this?

Expected answer:
- Provide explicit scope.
- Ask for a plan before implementation.
- Use scoped permissions.
- Use a feature branch.
- Review the diff frequently.
- Configure hooks or restrictions where necessary.
Scenario 3: Claude generates code that compiles but has concurrency problems.

Expected answer:
- Ask for a concurrency-focused review.
- Inspect shared mutable state.
- Review locking and transaction boundaries.
- Examine idempotency and retry behavior.
- Add concurrency and integration tests.
- Validate with profiling or load testing where relevant.
Scenario 4: You have a large monolith with hundreds of modules. How would you avoid context overload?

Expected answer:
- Use targeted code searches.
- Maintain module-specific instructions.
- Use path-scoped Rules.
- Delegate independent codebase exploration.
- Save architecture summaries.
- Use separate sessions for unrelated work.
Scenario 5: How would you use Claude Code in a CI/CD pipeline?

Possible workflow:
### Pull Request
    |
    v
CI Pipeline
    |
    v
Claude Code -p
    |
    v
### Analyze Diff
    |
    v
Security / Quality Review
    |
    v
### Structured Findings
    |
    v
### Human Review

Use non-interactive execution, controlled credentials and explicit permissions. Do not let an AI review automatically approve or deploy sensitive production changes.

## Hands-on practical interview exercises

An interviewer may ask you to demonstrate Claude Code rather than just explain it.
Exercise	What you should demonstrate
Explore an unfamiliar repository	Architecture discovery
Implement a **REST** endpoint	Multi-file implementation
Debug a failing test	Root-cause analysis
Create a **CLAUDE**.md	Project customization
Create a Skill	Reusable workflow
Create a Hook	Lifecycle automation
Create a security Subagent	Agent specialization
Connect an **MCP** server	External integration
Review a pull request	Engineering judgment
Refactor a legacy service	Incremental changes and verification

## Advanced questions for Senior / Principal Engineers

These questions distinguish someone who has experimented with Claude Code from someone who understands how to use it in a real engineering organization. ## How do you measure AI-generated code quality? ## How do you prevent prompt injection through repository files? ## How do you manage secrets when Claude executes shell commands? ## How do you prevent two agents from modifying the same files? ## How do you design a human-in-the-loop workflow? ## How do you manage context across long-running tasks? ## How do you prevent Claude from making unnecessary architectural changes? ## How do you integrate AI coding agents with enterprise Git workflows? ## How would you implement an AI-assisted code review pipeline? ## How do you evaluate the productivity improvement from AI coding agents? ## What are the limitations of agentic coding? ## How would you govern AI-assisted development across multiple engineering teams? A particularly important answer: Claude Code should accelerate engineering work, not replace engineering judgment. The developer remains responsible for architecture, correctness, security, testing and production readiness.

## Suggested preparation roadmap

A practical 7-day interview preparation plan
## Day 1 – Fundamentals
    
    Agent architecture, tool use, **CLI**, context window and sessions.
## Day 2 – Customization
    
    **CLAUDE**.md, Rules, Skills and project configuration.
## Day 3 – Automation
    
    Hooks, permissions, checkpoints and Git workflows.
## Day 4 – Multi-agent systems
    
    Subagents, Agent Teams, delegation and parallel execution.
## Day 5 – MCP
    
    **MCP** architecture, servers, tools, resources and authentication.
## Day 6 – Enterprise engineering
    
    Security, CI/CD, code reviews, large repositories and governance.
## Day 7 – Practical assessment
    
    Implement a feature in a Spring Boot project and demonstrate the entire workflow.

## Recommended study resources

These are useful references for preparing answers based on the current product documentation:
- Claude Code – Official documentation
- Claude Code Skills
- Claude Code Hooks
- Claude Code Subagents
- Claude Code **MCP**
- Permission modes
- Anthropic: Steering Claude Code

## Let's test your interview readiness

I recommend practising this as a mock interview rather than just reading questions. Claude Code mini assessment 5 questions

## What is the most important difference between a Skill and a Subagent?

A Skill is a reusable workflow; a Subagent is an independently delegated agent.

A Skill runs shell commands; a Subagent only reads files.

They are exactly the same.

A Subagent is a type of Hook.

## You need to ensure a formatter runs after every matching file edit. What should you use?

**CLAUDE**.md

A Skill

A PostToolUse Hook

A Subagent

## What is the main benefit of a Subagent's separate context window?

It increases the Git repository size.

It isolates delegated work and reduces clutter in the parent context.

It bypasses permissions.

It permanently remembers every conversation.

## What is MCP primarily used for?

Compacting context

Managing Git branches

Connecting Claude to external tools and data

Formatting Java code

## What is the safest workflow for a complex enterprise feature?

Immediately enable bypass permissions.

Let multiple agents edit the same files.

Plan, implement incrementally, test, review and inspect the diff.

Skip tests if Claude says the code is correct.

Submit answers
