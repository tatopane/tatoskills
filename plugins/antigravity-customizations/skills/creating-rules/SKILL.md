---
name: creating-rules
description: Guides the agent on how to create, format, and configure custom rules and workflows for Antigravity. Use when asked to define user rules, constraints, or repeatable workflows.
---

# Working with Rules and Workflows

This skill provides guidelines, specifications, and best practices for creating and configuring rules and workflows in Antigravity.

## When to Use This Skill
Use this skill when:
- The user requests to enforce specific coding styles, language requirements, or repository constraints.
- You need to create repeatable automation steps (workflows).
- You are documenting or debugging agent constraints.

---

## Antigravity Rules

Rules are manually defined constraints written in Markdown that guide the agent's behavior, coding style, and technology stack.

### 1. Limits and Scopes
- **Character Limit**: Rules files are strictly limited to **12,000 characters** each.
- **Global Rules**: Configured in `~/.gemini/GEMINI.md`. These apply universally across all workspaces.
- **Workspace Rules**: Located in `<workspace-root>/.agents/rules/` (retains backward compatibility with `.agent/rules/`). These only apply to the active workspace.

### 2. Rule Activation Modes
When creating a rule, you can configure how it is activated:
- **Manual**: The rule is only loaded when explicitly `@mentioned` by the user in the prompt.
- **Always On**: The rule is loaded for all invocations.
- **Model Decision**: The model evaluates a natural language description of the rule to decide whether to activate it for the current context.
- **Glob**: The rule is activated automatically when working on files matching a glob pattern (e.g., `*.js`, `src/**/*.ts`).

### 3. File References (`@ Mentions`)
Rules can reference other workspace files using the `@filename` syntax:
- Relative paths are resolved relative to the rules file location.
- Absolute paths (e.g., `@/path/to/file.md`) resolve to the true absolute path first. If not found, they resolve relative to the workspace root.

---

## Antigravity Workflows

Workflows are Markdown documents that define a series of steps to guide the agent through repetitive multi-step processes (e.g., deploying a service, running checks, or processing pull requests).

### 1. Workflow Mechanics
- **Invocation**: Triggered in the agent chat using the `/workflow-name` command.
- **Scoping**:
  - **Global**: Accessible from any workspace.
  - **Workspace**: Configured specifically for a single workspace root.
- **Nesting**: Workflows can call other workflows (e.g., `/workflow-1` can contain text instructing the agent to run `/workflow-2`).
- **File Format**: Written in Markdown, requiring a title, description, and structured steps. Subject to the **12,000 character** limit.

### 2. Agent-Generated Workflows
Agents can generate workflows automatically based on the active conversation history. This is ideal after successfully performing a complex multi-step task manually with the user.

---

## Best Practices

### 1. Choose the Right Rule Activation
- Use **Glob** patterns for language-specific style rules (e.g., python styling or web-app layout guidelines).
- Use **Model Decision** for high-level architectural rules (e.g., "Use clean architecture design").
- Use **Always On** for critical safety restrictions or corporate coding standards.

### 2. Keep Rules Focused
Do not create single, massive rules files. Split guidelines into smaller files under 12,000 characters and use Glob patterns or Model Decisions to trigger them only when needed to keep the agent's context window light.

### 3. Clear, Actionable Workflow Steps
Ensure workflow markdown steps are written in clear, imperative language (e.g., "1. Run npm test", "2. If tests fail, fix errors and repeat"). This reduces ambiguity when the agent executes the workflow trajectory.
