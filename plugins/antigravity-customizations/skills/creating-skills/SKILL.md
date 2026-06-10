---
name: creating-skills
description: Guides the agent on how to create, format, and structure new workspace-specific or global skills for Antigravity. Use when the user requests to create or define a new skill.
---

# Creating Antigravity Skills

This skill provides instructions and best practices for creating and structuring skills for Antigravity.

## When to Use This Skill
Use this skill when:
- The user requests to create a new skill (global or workspace-specific).
- The user wants to modify, document, or improve an existing skill.
- You need to structure custom agent capabilities or guidelines.

## Location of Skills
Antigravity supports two scopes for skills:

| Scope | Directory Path | Purpose |
| --- | --- | --- |
| **Workspace-specific** | `<workspace-root>/.agents/skills/<skill-folder>/` | Project-specific conventions, deployment processes, or testing rules. |
| **Global** | `~/.gemini/config/skills/<skill-folder>/` | Personal utilities, general-purpose tools, and configurations active across all projects. |

> [!NOTE]
> Antigravity defaults to `.agents/skills` for workspace skills, but maintains backward compatibility with `.agent/skills`.

## Structure of a Skill Folder
While only `SKILL.md` is required, a complete skill can contain helper resources:

```
.agents/skills/my-skill/
├── SKILL.md       # Main instructions (required)
├── scripts/       # Helper scripts and automation tools (optional)
├── examples/      # Reference implementations and examples (optional)
└── resources/     # Templates, schemas, and other assets (optional)
```

## Creating the SKILL.md File
Every skill must have a `SKILL.md` file containing YAML frontmatter at the very top.

### 1. Frontmatter Configuration
```yaml
---
name: skill-name-identifier
description: Third-person description of what the skill does. Include keywords that help the agent match it to user requests.
---
```
- **`name`**: (Optional) Unique lowercase identifier using hyphens (e.g., `code-review`). Defaults to the folder name if omitted.
- **`description`**: (Required) A clear description of the skill's capabilities. This is parsed by the agent at startup to determine relevance. Example: *"Reviews code changes for bugs, style issues, and best practices. Use when reviewing PRs or checking code quality."*

### 2. Instruction Content Structure
A well-structured `SKILL.md` file should include the following sections:
- **Title**: A clear heading (e.g., `# Code Review Skill`).
- **When to use**: Under what conditions the agent should activate the skill.
- **Step-by-step guidance**: Actionable instructions, checklist items, and workflow processes.
- **Best practices**: Constraints, safety guidelines, and optimization advice.

---

## Best Practices for Skill Creation

### 1. Keep Skills Focused
Each skill should do one thing well. Avoid creating "do-everything" skills. Instead, break complex tasks into smaller, focused skills (e.g., separate `django-testing` from `django-deployment`).

### 2. Write Clear Descriptions
The description is critical because it acts as the router. Use specific keywords and a clear, third-person format (e.g., "Generates unit tests using pytest" rather than "I can write tests").

### 3. Treat Scripts as Black Boxes
If your skill folder contains helper scripts in `scripts/`, instruct the agent to run them with the `--help` flag first rather than reading the entire script source code. This saves token context space.

### 4. Include Decision Trees
For complex tasks, include markdown tables or bullet points guiding the agent through different scenarios and what steps to take.

---

## Example: Code Review Skill

Below is a complete example of a simple `SKILL.md` file:

```markdown
---
name: code-review
description: Reviews code changes for bugs, style issues, and best practices. Use when reviewing PRs or checking code quality.
---

# Code Review Skill

When reviewing code, follow these steps:

## Review checklist

1. **Correctness**: Does the code do what it's supposed to?
2. **Edge cases**: Are error conditions handled?
3. **Style**: Does it follow project conventions?
4. **Performance**: Are there obvious inefficiencies?

## How to provide feedback

- Be specific about what needs to change
- Explain why, not just what
- Suggest alternatives when possible
```
