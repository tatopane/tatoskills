---
name: creating-plugins
description: Guides the agent on how to create, format, and structure custom plugins (both workspace-specific and global) for Antigravity. Use when the user requests to create or define a new plugin.
---

# Creating Antigravity Plugins

This skill provides instructions, structural guidelines, and best practices for creating and packaging plugins for Antigravity.

## When to Use This Skill
Use this skill when:
- The user asks to create a new plugin (global or workspace-specific).
- The user wants to modify, inspect, or document an existing plugin.
- You need to package multiple custom skills, rules, or tool configurations (MCP servers/hooks) into a single, cohesive bundle.

## What is a Plugin?
Plugins are namespaced bundles that allow you to extend Antigravity's capabilities by grouping skills, rules, MCP servers, and hooks into a single package.

## Directory Structure
A plugin is a folder containing a mandatory `plugin.json` manifest file at its root, along with optional subdirectories and configurations for different extension types:

```
plugins/<plugin-name>/
├── plugin.json       # Required manifest file (identifies the directory as a plugin)
├── mcp_config.json   # Optional MCP server definitions (connects external tools)
├── hooks.json        # Optional hooks definition (executes scripts on specific events)
├── skills/           # Optional custom skills
│   └── <skill-name>/
│       └── SKILL.md
└── rules/            # Optional custom rules
    └── <rule-name>.md
```

### The Manifest File (`plugin.json`)
Every plugin must have a `plugin.json` at its root. This acts as the marker file.
```json
{
  "name": "my-custom-plugin"
}
```
- **`name`**: (Optional) The name of the plugin. Defaults to the folder name if omitted.

---

## Supported Components

1. **Skills (`skills/`)**
   - Place custom skills inside `skills/<skill-name>/`.
   - Each skill must contain a `SKILL.md` with instructions and YAML frontmatter (`name` and `description`).
2. **Rules (`rules/`)**
   - Rules are markdown files placed in the `rules/` directory (e.g., `rules/my-rule.md`).
   - They specify strict constraints or formatting guidelines for the agent's behavior.
3. **MCP Servers (`mcp_config.json`)**
   - Configures connection info to Model Context Protocol (MCP) servers.
   - Allows the agent to interact with external tools and databases.
4. **Hooks (`hooks.json`)**
   - Defines scripts or commands to run automatically on specific lifecycle events (e.g., post-command execution, post-file modification).

---

## Plugin Scopes and Installation Paths

Antigravity automatically scans specific directories to discover and load plugins:

| Scope | Installation Directory | Availability |
| --- | --- | --- |
| **Workspace-specific** | `<workspace-root>/.agents/plugins/` <br> `<workspace-root>/_agents/plugins/` | Only when working inside that specific workspace. |
| **Global** | `~/.gemini/config/plugins/` | Active across all workspaces on the user's machine. |

---

## Best Practices for Plugin Development

### 1. Scope Appropriately
- Use **Workspace-level plugins** for project-specific development guidelines, team rules, local test suite hooks, or local database MCP configurations.
- Use **Global-level plugins** for personal productivity tools, general-purpose scripts, and universal rule constraints.

### 2. Follow Naming Conventions
Keep plugin folder names and the manifest `"name"` field consistent, using lowercase alphanumeric characters and hyphens (e.g., `git-helper-plugin`).

### 3. Minimize Startup Overhead
Since Antigravity scans and loads plugin configurations at startup, ensure `plugin.json` is lightweight, and avoid bloated MCP configurations or slow hook processes that might increase load time.

### 4. Cohesive Packaging
Group related capabilities. For example, if you build a database helper plugin, package:
- The database querying MCP server in `mcp_config.json`.
- A query checklist skill in `skills/query-checklist/SKILL.md`.
- A rule enforcing transaction blocks in `rules/require-transactions.md`.
