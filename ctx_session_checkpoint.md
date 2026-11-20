Claude Code: Context Window, Session Management and Checkpoints
These three concepts are closely related, but they solve different problems:
- Context window: How much information Claude can use in the current conversation.
- Session management: How you save, continue, resume or separate conversations.
- Checkpoints: How you recover from unwanted code changes and rewind conversation state.
Understanding them is important when using Claude Code for large Java/Spring Boot projects or multi-day implementation tasks.
## Context window
The context window is the amount of information Claude can consider while responding.

It can contain:
- Your prompts and Claude's responses.
- Source files Claude has read.
- Terminal commands and their outputs.
- Tool calls and results.
- **CLAUDE**.md project instructions.
- Relevant conversation history.
Example: Suppose you ask Claude to investigate an authentication issue. It reads 15 Java classes, several configuration files and test logs. All that information contributes to context usage.

As a session grows, the context can become crowded with information that is no longer relevant.
Important context commands
Command	Purpose
/context	Inspect current context usage.
/compact	Summarize conversation history to free context.
/clear	Clear the current conversation and start fresh.
/help	See available commands.

/compact vs /clear /compact — Continue the same task

Claude summarizes the conversation, retaining important information while reducing the amount of context used.

Example: /compact Preserve the architecture, implementation decisions, files modified, test results and outstanding issues. Remove unrelated exploration and verbose command output.

/clear — Start a different task

Clears the active conversation context. Project files and **CLAUDE**.md remain available.

Example:

You have completed a Spring Security **LDAP** issue and now want to start an unrelated Kafka consumer implementation.

Use: /clear

| Feature | /compact | /clear |
| --- | --- | --- |
| Current conversation | Summarized | Cleared |
| Important previous context | Retained in summary | Not carried forward |
| Project files | Remain | Remain |
| **CLAUDE**.md | Remains applicable | Remains applicable |
| Best for | Continuing a task | Starting a new task |

Rule: Compact to continue; clear to switch tasks. ## Session management A session is an individual Claude Code conversation. It maintains its own conversation history and task context.

For example, you might maintain separate sessions for:
- spring-security-ldap
- customer-api-development
- kafka-consumer-optimization
- cassandra-performance
Starting and resuming sessions
# Start a new session
claude

# Continue the most recent session

claude --continue

# Open the session picker

claude --resume

# Resume a particular session

claude --resume <session-id>

You can also rename the current session: /rename spring-security-ldap

This makes it easier to identify sessions when you have several ongoing tasks.
Session management commands
Command	Purpose
claude	Start a new session
claude --continue	Continue the latest session
claude --resume	Select a previous session
claude --resume <session-id>	Resume a particular session
/rename	Give the current session a meaningful name
/clear	Clear active conversation context
/exit	Exit Claude Code

Session vs project memory
These are different things:
Session history	Project instructions
Contains conversation and task history	Contains durable project knowledge
Specific to a particular task	Shared across relevant sessions
Can be resumed	Loaded from project instruction files
May contain temporary decisions	Should contain stable architecture and coding conventions

For example, suppose you are working on a Spring Boot application.

Your **CLAUDE**.md should contain:
- Application architecture.
- Java and Spring Boot versions.
- Coding conventions.
- Database and Kafka conventions.
- Build and test commands.
Your session should contain:
- The specific bug you are fixing.
- Files you investigated.
- Implementation decisions.
- Current progress.
- Remaining tasks.
Example: Multiple sessions
Spring Boot project
**CLAUDE**.md
Shared context

Architecture, coding rules and project conventions.

Session 1: **LDAP** authentication Resumable

**LDAP** provider, database fallback, timeout handling. Session 2: Kafka consumer Resumable

Consumer groups, retries, dead-letter handling. Session 3: Cassandra optimization Resumable

Partition keys, query patterns and read performance.

The benefit is that you don't need to carry the entire history of **LDAP** debugging into a new Kafka task. ## Checkpoints and rewind Checkpoints help you recover from unwanted changes made during a Claude Code session.

Think of a checkpoint as a recovery point associated with a conversation and the code changes Claude Code tracks. How checkpoints work Claude Code automatically creates checkpoints around user turns. It can track file edits made through its supported editing tools.

Suppose you ask Claude: ## Explore the authentication implementation. ## Modify the authentication provider. ## Update the security configuration. ## Add unit tests. If the implementation goes in the wrong direction, you can use a checkpoint to return to an earlier state. Rewind command /rewind

You can also press Esc twice when the prompt input is empty to open the rewind interface in supported terminal sessions.

The rewind interface lets you select an earlier conversation point and choose an appropriate recovery action.
Action	Behaviour
Restore code and conversation	Restore tracked code changes and rewind conversation
Restore conversation	Rewind conversation while keeping current code
Restore code	Restore tracked code changes while keeping conversation
Summarize from here	Summarize the conversation from the selected point forward
Summarize up to here	Summarize earlier conversation while keeping later messages

The exact options depend on the selected checkpoint and Claude Code version.
Practical example
Suppose you ask Claude to implement **LDAP** authentication with database fallback.
## Checkpoint 1 — Before implementation
    
    Claude has explored the existing Spring Security configuration.
## Checkpoint 2 — Authentication provider modified
    
    Claude changes the authentication provider and configuration.
## Checkpoint 3 — Problem discovered
    
    Existing database authentication no longer works.
## Rewind
    
    Run /rewind and select the checkpoint before the problematic changes.
## Continue
    
    Ask Claude to implement a different approach while preserving the existing authentication flow.
Important: Rewind is not a complete filesystem backup. Changes made by external scripts, IDEs or other processes may not be included. It also cannot undo external side effects such as database writes, deployments or **API** calls.
## Checkpoints vs Git
This is an important distinction.

Claude Code checkpoints are useful for quickly recovering from unwanted changes. Git is for durable source-code history and collaboration.
| Capability | Claude checkpoint | Git |
| --- | --- | --- |
| Recover recent Claude edits | Yes, for tracked edits | Yes, through Git operations |
| Restore conversation | Yes | No |
| Maintain permanent code history | No | Yes |
| Share changes with team | No | Yes |
| Undo database changes | No | No |
| Compare code versions | Limited | Yes |

Recommended Git practice Before asking Claude to make a large change, save a known-good state. # Check current changes git status

# Create a feature branch

git switch -c feature/customer-pagination

# Save a baseline, if the working tree is ready

git add . git commit -m *chore: save baseline before pagination*

Only stage files you have reviewed. Don't accidentally include unrelated changes.

If Claude makes an unwanted change, use /rewind for a quick recovery or Git to restore a known-good code state.
## Practical workflow for a multi-day development task
Consider implementing a new **API** in your Spring Boot application. It may take several hours or multiple sessions.
Recommended development lifecycle
## Explore — Plan mode
    
    Understand existing classes, dependencies, **API** flow and database structure.
    Read-only investigation
## Design — Plan mode
    
    Create an implementation plan and identify affected files.
## Implement — Accept Edits
    
    Make code changes and write tests.
    Implementation
## Verify — Review
    
    Run tests, inspect the diff and fix problems.
## Compact — Preserve context
    
    If the task is continuing but the context is getting large, run /compact.
## Resume — Continue later
    
    Use claude --continue or claude --resume to continue the task.
## Complete — Git commit
    
    Commit the verified implementation.

## Handoff between sessions

For work spanning multiple days, I recommend maintaining a handoff document.

For example: project/

**CLAUDE**.md

docs/development/

customer-pagination-handoff.md

Before ending a long session, ask Claude: Create or update a development handoff document.

Include: ## Feature objective ## Existing architecture ## Implementation completed ## Important design decisions ## Files modified ## Tests executed and results ## Outstanding issues ## Exact next steps

Do not include credentials or secrets.

When you return: Read the development handoff document.

Inspect the current Git status and diff. Understand what has already been implemented. Summarize the current state and propose the next steps.

Do not modify files until I approve the approach.

This is particularly useful when a feature takes several days or when you need to move between development machines.
## Important best practices
Situation	What I recommend
Context is getting large	Use /compact
Starting an unrelated feature	Use /clear
Continuing yesterday's work	Use claude --resume
Claude implemented the wrong approach	Use /rewind
Large implementation	Create a Git branch and baseline commit
Multi-day implementation	Maintain a handoff document
Need to understand context consumption	Use /context
Need to reduce unnecessary context	Delegate focused exploration to subagents when appropriate

A few cautions:
- Compaction is a summary, not a perfect preservation of every detail.
- Checkpoints do not capture every external file or system change.
- /clear does not delete your project files.
- Resuming a session does not guarantee that background processes or external operations are still running.
- Avoid putting enormous build logs or entire generated files into the conversation unnecessarily.
## Quick command reference
Command	Description
claude	Start Claude Code
claude --continue	Continue latest session
claude --resume	Select an existing session
claude --resume <session-id>	Resume a particular session
/rename <name>	Rename current session
/context	Inspect context usage
/compact	Compress conversation context
/clear	Clear active conversation
/rewind	Open checkpoint recovery
/diff	Review changes, where supported
/help	View available commands