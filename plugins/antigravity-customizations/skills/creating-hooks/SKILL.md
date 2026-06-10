---
name: creating-hooks
description: Guides the agent on how to write, configure, and structure lifecycle hooks in hooks.json for Antigravity. Use when asked to create, debug, or configure execution loop hooks.
---

# Working with Antigravity Hooks

This skill provides configuration guidelines, schema definitions, and best practices for creating and managing execution loop hooks in Antigravity.

## When to Use This Skill
Use this skill when:
- The user requests to run automated scripts or validations during specific lifecycle events.
- You need to build custom safety gates, linters, or diagnostics.
- You are debugging hook execution or writing custom hook scripts.

---

## What are Hooks?
Hooks are custom scripts or shell commands executed at specific points during Antigravity's loop. They receive execution context via `stdin` as JSON and communicate decisions or actions back via `stdout` as JSON.

---

## Configuration Location and Format

Hooks are defined inside a `hooks.json` file in one of the configuration folders:
- **Workspace Scope**: `.agents/hooks.json` or `_agents/hooks.json`
- **Global Scope**: `~/.gemini/config/hooks.json`

### Example `hooks.json`
```json
{
  "my-linter-hook": {
    "enabled": true,
    "PostToolUse": [
      {
        "matcher": "run_command",
        "hooks": [
          {
            "type": "command",
            "command": "./scripts/lint.sh",
            "timeout": 10
          }
        ]
      }
    ]
  },
  "reminder": {
    "PreInvocation": [
      {
        "command": "./scripts/reminder.sh"
      }
    ]
  }
}
```

---

## Lifecycle Events & Matchers

| Event | Trigger Point | Matcher Required? |
| --- | --- | --- |
| `PreToolUse` | Fires before a tool is executed. | **Yes** (Matches tool name) |
| `PostToolUse` | Fires after a tool completes execution. | **Yes** (Matches tool name) |
| `PreInvocation` | Fires before Antigravity sends the conversation history to the model. | No (Matcher is ignored) |
| `PostInvocation` | Fires after tool calls finish executing. | No (Matcher is ignored) |
| `Stop` | Fires when the agent's main execution loop terminates. | No (Matcher is ignored) |

### Gating Tools with Matchers
For `PreToolUse` and `PostToolUse`, you must use a regular expression in the `matcher` field to filter which tools trigger the hook:
- `""` or `"*"`: Matches all tools.
- `"run_command"`: Matches exactly `run_command`.
- `"run_command|write_to_file"`: Matches either tool.
- `"browser_.*"`: Matches any tool starting with `browser_`.

---

## Input & Output Contracts (JSON)

All hooks receive common metadata fields on `stdin`:
- `conversationId`: UUID of the active conversation.
- `workspacePaths`: List of absolute paths of mounted workspaces.
- `transcriptPath`: Path to the persistent `transcript.jsonl` log file.
- `artifactDirectoryPath`: Path to the conversation's artifact output folder.

### 1. PreToolUse
- **Input (`stdin`)**: Contains `toolCall` (name and args) and `stepIdx`.
- **Output (`stdout`)**:
  - `decision` (string, required): 
    - `"allow"`: Automatically allow execution.
    - `"deny"`: Hard block the tool execution.
    - `"ask"`: Ask user for permission (respects "Always Allow").
    - `"force_ask"`: Always prompt user, bypassing cached rules.
  - `reason` (string, optional): Message shown to user/agent.
  - `permissionOverrides` (array of strings, optional): Custom resource strings.

*Example Output:*
```json
{
  "decision": "ask",
  "reason": "Verify safety before running a test script.",
  "permissionOverrides": ["command(npm test)"]
}
```

### 2. PostToolUse
- **Input (`stdin`)**: Contains `stepIdx` and an optional `error` message string if the tool failed.
- **Output (`stdout`)**: Returns an empty JSON object `{}`.

### 3. PreInvocation
- **Input (`stdin`)**: Contains `invocationNum` and `initialNumSteps`.
- **Output (`stdout`)**:
  - `injectSteps` (array, optional): A list of steps to inject before the model runs.
    - Each step can inject a `toolCall` (object), a `userMessage` (string), or an `ephemeralMessage` (string).

*Example Output:*
```json
{
  "injectSteps": [
    {
      "ephemeralMessage": "Remember to run linter before finishing."
    }
  ]
}
```

### 4. PostInvocation
- **Input (`stdin`)**: Same as `PreInvocation`.
- **Output (`stdout`)**:
  - `injectSteps` (array, optional): Same format as `PreInvocation`.
  - `terminationBehavior` (string, optional): Set to `"force_continue"` to force another loop run, `"terminate"` to stop, or `""` for default.

### 5. Stop
- **Input (`stdin`)**: Contains `executionNum`, `terminationReason`, `error`, and `fullyIdle` (boolean representing if background tasks are finished).
- **Output (`stdout`)**:
  - `decision` (string, required): Set to `"continue"` to prevent the agent from stopping and re-enter the loop.
  - `reason` (string, optional): Injected system message if continuing.

---

## Best Practices for Writing Hooks

### 1. Ensure Scripts are Fast
Hooks block the agent's execution. Always include a realistic `"timeout"` (defaults to 30 seconds) in your hook definition, and write optimized scripts that complete in milliseconds where possible.

### 2. Graceful Error Handling
If a hook script fails or throws an exception, it can block the agent loop. Ensure your hook scripts catch errors internally, exit cleanly, and write logs to stderr if diagnostics are needed.

### 3. Restrict Matchers Narrowly
Do not use `"*"` matchers unless absolutely necessary. For example, if you want to lint files after write operations, match `"write_to_file|replace_file_content|multi_replace_file_content"` specifically instead of all tools.
