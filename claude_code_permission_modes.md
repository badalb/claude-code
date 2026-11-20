# Claude Code Permission Modes

## 1. What are permission modes?

Claude Code permission modes control **how much autonomy Claude has when
using tools and making changes**.

They determine whether Claude: - Asks before editing files or running
commands. - Can automatically make certain changes. - Is restricted to
read-only planning. - Uses additional safety checks for tool actions. -
Bypasses normal permission prompts.

Permission modes are **not model or thinking modes**. They govern tool
permissions and approval behaviour.

## 2. Permission modes at a glance

  ---------------------------------------------------------------------------------
  Mode              Setting value         Behaviour               Typical use
  ----------------- --------------------- ----------------------- -----------------
  **Manual**        `default`             Claude asks for         Safest
                                          approval when an action general-purpose
                                          requires permission.    mode

  **Accept Edits**  `acceptEdits`         Automatically accepts   Implementing
                                          supported file edits    features and
                                          and common filesystem   tests
                                          operations in permitted 
                                          working areas; other    
                                          actions may still       
                                          require approval.       

  **Plan**          `plan`                Read-only exploration   Understanding a
                                          and planning; does not  codebase and
                                          make source-code        designing a
                                          changes.                solution

  **Auto**          `auto`                Uses an additional      Longer tasks
                                          safety-classification   where fewer
                                          process to decide       approval
                                          whether tool actions    interruptions are
                                          can proceed.            useful

  **Don't Ask**     `dontAsk`             Does not prompt for     Restricted,
                                          permission; actions not pre-approved
                                          already allowed are     workflows
                                          denied.                 

  **Bypass          `bypassPermissions`   Bypasses normal         Only isolated,
  Permissions**                           permission checks.      disposable
                                                                  environments
  ---------------------------------------------------------------------------------

Availability and exact behaviour can depend on the Claude Code version,
account, model and organization policy. In particular, Auto mode may not
be enabled in every environment.

## 3. Understanding each mode

### Manual (`default`)

This is the cautious, approval-based mode.

-   Claude can inspect files and reason about the code.
-   It asks for approval before actions that require permission.
-   You remain in control of potentially risky operations.

Use it when working with sensitive code, security configuration,
production-related changes or unfamiliar repositories.

### Accept Edits (`acceptEdits`)

This mode reduces interruptions while Claude is implementing changes.

-   Supported edits are automatically accepted within the applicable
    permitted areas.
-   Some common filesystem operations may also be accepted.
-   Commands and actions outside the accepted permissions can still
    require approval.

Use it for normal development tasks such as implementing a Spring Boot
API, updating tests or refactoring a service. Review the diff and run
tests before committing.

### Plan (`plan`)

Plan mode is intended for investigation and design before
implementation.

Claude can: - Explore project structure and source files. - Trace API
flows and dependencies. - Identify likely files that need changes. -
Propose an implementation approach. - Identify risks and test scenarios.

It does not make source-code changes while operating in this mode.

Use it before asking Claude to implement a large feature or modify an
unfamiliar codebase.

### Auto (`auto`)

Auto mode adds a safety-classification step to tool use. It is designed
to reduce repeated permission prompts while retaining a safety layer.

It is not equivalent to bypassing permissions. Availability and
supported behaviour can vary by version and account or organization
settings.

Use it for longer-running tasks when it is available and appropriate,
while still reviewing the resulting changes.

### Don't Ask (`dontAsk`)

This mode avoids interactive permission prompts.

-   Actions already permitted can proceed.
-   Actions that would require additional permission are denied rather
    than presented for approval.

This is different from Accept Edits: it does not mean that all edits or
commands are automatically approved.

Use it only when the allowed actions have been deliberately configured
in advance.

### Bypass Permissions (`bypassPermissions`)

This mode bypasses normal permission checks.

**Use extreme caution.** Claude may perform actions without the usual
approval prompts. A mistake can modify or delete files, run unwanted
commands or affect external systems.

Use it only in a suitably isolated, disposable container or virtual
machine---not in your everyday development environment, a
production-connected shell or a repository containing important
uncommitted work.

## 4. Switching modes

In an interactive Claude Code session, use **Shift+Tab** to cycle
through the permission modes available in your environment. The exact
cycle can vary by version and configuration.

You can also start Claude Code with a selected mode:

``` bash
# Plan / read-only exploration
claude --permission-mode plan

# Automatically accept supported edits
claude --permission-mode acceptEdits

# Approval-based mode
claude --permission-mode default

# Auto mode, if enabled for your environment
claude --permission-mode auto

# Don't Ask mode
claude --permission-mode dontAsk

# Bypass mode — use only in an isolated environment
claude --permission-mode bypassPermissions
```

A shortcut for bypass mode is:

``` bash
claude --dangerously-skip-permissions
```

Do not use the bypass option as a convenience setting for normal
development.

## 5. Configure a default permission mode

You can configure a default mode in Claude Code settings. For example,
in `~/.claude/settings.json`:

``` json
{
  "permissions": {
    "defaultMode": "acceptEdits"
  }
}
```

Choose a default that matches your risk tolerance and working
environment. Check the current Claude Code documentation for the
supported settings and precedence rules in your installed version.

## 6. Recommended workflow for enterprise Java / Spring Boot development

A practical workflow is to change modes as the task moves through its
lifecycle.

  -----------------------------------------------------------------------
  Stage                   Recommended mode        What to ask Claude to
                                                  do
  ----------------------- ----------------------- -----------------------
  Explore                 Plan                    Understand the existing
                                                  API, service layers,
                                                  repositories, security
                                                  and tests.

  Design                  Plan                    Propose the
                                                  implementation,
                                                  affected files, API
                                                  contract and test plan.

  Implement               Accept Edits            Make the agreed code
                                                  and test changes.

  Investigate sensitive   Manual                  Review security,
  changes                                         authentication,
                                                  authorization, database
                                                  migrations and
                                                  infrastructure
                                                  commands.

  Verify                  Manual or Accept Edits  Run tests and inspect
                                                  failures; approve
                                                  commands as
                                                  appropriate.

  Final review            Manual                  Review the diff, check
                                                  for regressions and
                                                  decide what to commit.
  -----------------------------------------------------------------------

Example prompts:

**Explore the codebase**

> Use Plan mode. Trace the complete request flow for the customer API,
> from controller to service to repository. Do not modify any files.
> Summarize the architecture and relevant tests.

**Design a feature**

> Stay in Plan mode. Design an implementation for adding pagination to
> the customer search API. Identify affected classes, API changes,
> database implications, edge cases and test cases. Do not edit files.

**Implement the approved plan**

> Implement the agreed pagination design. Follow existing project
> conventions, add unit and integration tests, and avoid unrelated
> refactoring.

**Review the changes**

> Review the complete diff as a Principal Engineer. Look for
> correctness, security, backward compatibility, performance,
> transaction handling and missing tests. Do not modify files; report
> findings by severity.

## 7. Common misunderstandings

  -----------------------------------------------------------------------
  Misunderstanding                    Correct interpretation
  ----------------------------------- -----------------------------------
  Plan mode is a model that thinks    It is a permission mode that
  more deeply.                        restricts actions to read-only
                                      planning.

  Accept Edits approves every         It automatically accepts supported
  terminal command.                   edits; other actions can still
                                      require approval.

  Don't Ask means approve everything. It denies actions that are not
                                      already permitted instead of
                                      asking.

  Auto is the same as bypass.         Auto adds a safety-classification
                                      layer; bypass skips normal
                                      permission checks.

  Bypass is the fastest recommended   It removes important safeguards and
  development mode.                   should be restricted to isolated
                                      environments.

  The trigger for a permission prompt Behaviour can depend on tool type,
  is always the same.                 configured permissions, mode,
                                      version and organization policy.
  -----------------------------------------------------------------------

## 8. Official documentation

-   [Claude Code permission
    modes](https://code.claude.com/docs/en/permission-modes)
-   [Claude Code
    permissions](https://code.claude.com/docs/en/permissions)
-   [Claude Code documentation
    overview](https://code.claude.com/docs/en/overview)

**Key takeaway:** Use Plan to understand and design, Accept Edits to
implement, and Manual mode when you want explicit control over sensitive
actions. Treat Auto as a safety-assisted convenience, Don't Ask as
deny-unless-preapproved, and Bypass as an exceptional option for
isolated environments.
