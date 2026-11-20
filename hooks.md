## Claude Code Hooks

## What are Hooks?

Hooks are automated actions that Claude Code executes when specific events occur during a coding session.

Unlike **CLAUDE**.md, Rules and Skills, Hooks are designed to execute actual commands or other configured handlers automatically.

Think of Hooks as automation and enforcement mechanisms.

For example, you can configure Claude Code to:
- Automatically format Java files after editing.
- Run unit tests after modifying source code.
- Block modifications to production configuration files.
- Run static code analysis.
- Display Git status whenever a new session starts.
- Verify that tests pass before Claude considers a task complete.
The important distinction is that a Hook executes because an event occurred, not because Claude remembered an instruction. 
Claude Code Docs+1

## Rules vs Skills vs Hooks

| Feature | Responsibility | Example |
| --- | --- | --- |
| **CLAUDE**.md | Project context | Application uses Java 21 |
| Rules | Coding standards | Use constructor injection |
| Skills | Reusable workflows | Implement a **REST** **API** |
| Hooks | Automatic execution | Run formatter after editing |
| Subagents | Delegate independent work | Security code reviewer |

A simple way to remember:
- Rules = What to follow.
- Skills = How to perform a task.
- Hooks = What to execute automatically.
## How Hooks work
Consider this requirement:

Whenever Claude modifies a Java file, automatically run the formatter. Claude edits a Java file

PostToolUse Hook

Triggered after the Edit or Write tool completes successfully.

Run formatter

Execute google-java-format or another configured formatter.

Formatted source file

This automation happens without you explicitly asking Claude to format the file. ## Where do Hooks live? Hooks are configured in **JSON** settings files. my-spring-boot-project/ │ ├── **CLAUDE**.md │ ├── .claude/ │   ├── settings.json │   ├── settings.local.json │   │ │   ├── rules/ │   └── skills/ │ └── src/

There are three common configuration locations:
File	Scope
~/.claude/settings.json	All your projects
.claude/settings.json	Shared project configuration
.claude/settings.local.json	Local project configuration

Use project settings for team-wide automation and local settings for personal experiments. Claude by Anthropic+1

## Important Hook events

Here are eight useful lifecycle events to understand first. Claude Code has additional events as well, so this is not the complete event catalogue.
| Hook | When it executes | Typical use |
| --- | --- | --- |
| PreToolUse | Before a tool executes | Block dangerous commands |
| PostToolUse | After a tool succeeds | Format modified files |
| PermissionRequest | Before a permission dialog | Custom permission decisions |
| SessionStart | When a session starts or resumes | Load Git status |
| UserPromptSubmit | When you submit a prompt | Inject project context |
| Stop | When Claude finishes responding | Check task completion |
| SubagentStop | When a subagent finishes | Validate delegated work |
| PreCompact | Before context compaction | Preserve session information |

These are particularly useful for building a reliable development workflow. Claude by Anthropic+1

## Example 1: Automatically format Java code

Useful for everyday development Whenever Claude edits or creates a file, run the project's configured formatter.

File: .claude/settings.json
{
    *hooks*: {
    *PostToolUse*: [
    {
    *matcher*: *Write|Edit*,
    *hooks*: [
    {
    *type*: *command*,
    *command*: *./mvnw -q spotless:apply*,
    *timeout*: **120**
    }
    ]
    }
    ]
    }
}

How it works: ## Claude creates or edits a file. ## PostToolUse fires. ## The Maven Spotless formatter runs. ## The code is formatted automatically. Prerequisite: Configure the Spotless Maven plugin in your project. This example formats the project, not just the edited file, so it may be expensive for large repositories. ## Example 2: Block dangerous commands Useful security guardrail Suppose you don't want Claude to execute destructive commands such as rm -rf.

Create:

.claude/hooks/block-rm.sh #!/bin/bash

**INPUT**=$(cat) **COMMAND**=$(echo *$**INPUT*** | jq -r '.tool_input.command // "*')

if echo *$**COMMAND**" | grep -q 'rm -rf'; then
    jq -n '{
    hookSpecificOutput: {
    hookEventName: *PreToolUse*,
    permissionDecision: *deny*,
    permissionDecisionReason: *Destructive rm -rf command blocked*
    }
    }'
fi

Make it executable: chmod +x .claude/hooks/block-rm.sh

Configure .claude/settings.json:
{
    *hooks*: {
    *PreToolUse*: [
    {
    *matcher*: *Bash*,
    *hooks*: [
    {
    *type*: *command*,
    *command*: *${CLAUDE_PROJECT_DIR}/.claude/hooks/block-rm.sh*
    }
    ]
    }
    ]
    }
}

How it works:
- PreToolUse executes before a Bash command.
- The script reads the proposed command from **JSON** input.
- If it detects rm -rf, it returns a deny decision.
- Claude Code blocks that tool call.
This is an illustrative safeguard, not a complete security boundary. Use Claude Code permissions and operating-system controls for stronger protection. 
### Claude Code Docs

## Example 3: Load Git information when a session starts

Suppose every time you start Claude Code, you want it to know:
- Current Git branch.
- Uncommitted changes.
- Recent commits.
- Pending **TODO** items.
Configure:
{
    *hooks*: {
    *SessionStart*: [
    {
    *hooks*: [
    {
    *type*: *command*,
    *command*: "git status --short --branch && echo '--- **RECENT** **COMMITS** ---' && git log -5 --oneline && echo '--- **TODO** ---' && cat **TODO**.md 2>/dev/null || true"
    }
    ]
    }
    ]
    }
}

Claude receives the command's stdout as additional session context.

This is particularly useful when working on a large Spring Boot application across multiple sessions.
## Example 4: Run tests when Claude finishes a task
Suppose you want Claude to verify the project before completing its response.
{
    *hooks*: {
    *Stop*: [
    {
    *hooks*: [
    {
    *type*: *command*,
    *command*: *./mvnw -q test*,
    *timeout*: **120**
    }
    ]
    }
    ]
    }
}

This is a simple example, but it has an important limitation: the Stop hook can run after every assistant turn, including turns where no code was changed.

For more sophisticated completion checks, use a prompt-based Stop hook:
{
    *hooks*: {
    *Stop*: [
    {
    *hooks*: [
    {
    *type*: *prompt*,
    *prompt*: "Review whether the coding task is complete. Check whether required tests and verification have been performed. If work remains, return a decision to continue and explain what is missing. Otherwise allow the response to finish."
    }
    ]
    }
    ]
    }
}

A prompt-based hook uses a model to evaluate the condition; it is not a deterministic test runner. For mandatory test execution, use a command hook or CI pipeline. ### Claude Code Docs

## Example 5: Inject project context whenever you submit a prompt

Suppose you maintain a file called DEVELOPMENT_CONTEXT.md containing current sprint priorities and architectural decisions.
{
    *hooks*: {
    *UserPromptSubmit*: [
    {
    *hooks*: [
    {
    *type*: *command*,
    *command*: *cat DEVELOPMENT_CONTEXT.md*
    }
    ]
    }
    ]
    }
}

Now every submitted prompt gets additional context from that file.

This can be useful for frequently changing project information that you don't want to hardcode in **CLAUDE**.md.

Be careful with the size of this file because it is injected repeatedly. ## How does a Hook communicate with Claude Code? Command hooks receive **JSON** input through standard input (stdin).

For example, a PreToolUse hook may receive:
{
    *session_id*: *session-**123***,
    *hook_event_name*: *PreToolUse*,
    *tool_name*: *Bash*,
    *tool_input*: {
    *command*: *mvn clean install*
    }
}

There are three important outcomes:
Outcome	Meaning
Exit code 0	Hook succeeded; no decision unless output provides one
Exit code 2	Blocking error for events that support exit-code blocking
Other exit codes	Generally non-blocking error

Hooks can also return structured **JSON** to approve, deny, modify certain tool inputs, or provide context, depending on the event.

Do not confuse a successful hook execution with approval of a tool call. For example, a silent successful PreToolUse hook leaves the normal permission flow in place. Claude Code Docs+1

## Advanced: Different types of Hook handlers

Hooks are not limited to shell scripts.
| Type | What it does | Example |
| --- | --- | --- |
| command | Executes a local command or script | Run Maven tests |
| prompt | Asks a model to evaluate a condition | Review task completion |
| agent | Runs an agent to investigate a condition | Validate code changes |
| http | Sends event information to an **HTTP** endpoint | Notify an internal service |
| mcp_tool | Calls a configured **MCP** tool | Trigger a security scanner |

For most developers, start with command. Add the other types when you have a clear use case. ### Claude Code Docs

## How to manage and debug Hooks

Inside Claude Code, use: /hooks

This opens the Hook configuration browser.

Other useful commands: /status

Check the active configuration and settings sources. claude --debug

Start a session with debugging enabled to investigate Hook execution.

Changes to settings files are generally picked up automatically. ### Claude Code Docs

## Recommended Hooks for your Spring Boot projects

I would initially configure just these four:
| Priority | Hook | Purpose |
| --- | --- | --- |
| 1 | PostToolUse | Run formatter after source changes |
| 2 | PreToolUse | Block dangerous shell operations |
| 3 | SessionStart | Load Git status and project context |
| 4 | Stop | Verify task completion |

Avoid running a full Maven build after every individual file edit. Use targeted checks during development and a complete build before completing a feature. ## How all Claude Code customization features fit together **CLAUDE**.md

Project context: Java 21, Spring Boot 3, Maven, microservices.

Rules

Standards: constructor injection, security, exception handling.

Skills

Workflows: implement **API**, debug application, review code.

Hooks

Automation: format files, run checks, block risky operations.

Subagents

Delegation: independent security, performance and architecture reviews.
