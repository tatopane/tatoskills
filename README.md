# tato-public-skills

Welcome! This repository is my personal set of custom skills and plugins for Google Antigravity. It is designed to extend agent capabilities, automate repetitive tasks, and define custom guidelines for development.

## 🔌 Plugins

### 1. [antigravity-customizations](file:///Users/tatopane/tato-public-skills/plugins/antigravity-customizations)
A unified plugin containing helper skills that guide developer and agent interactions when creating custom extensions for Antigravity.

---

## 🛠️ Skills Included

The **antigravity-customizations** plugin contains the following six instructional skills:

| Skill Name | Path | Description |
| --- | --- | --- |
| **`creating-skills`** | [SKILL.md](file:///Users/tatopane/tato-public-skills/plugins/antigravity-customizations/skills/creating-skills/SKILL.md) | Guidance and best practices for creating and structuring custom skills. |
| **`creating-plugins`** | [SKILL.md](file:///Users/tatopane/tato-public-skills/plugins/antigravity-customizations/skills/creating-plugins/SKILL.md) | Guidelines on packaging multiple customizations (skills, rules, MCP, hooks) into a plugin. |
| **`creating-subagents`** | [SKILL.md](file:///Users/tatopane/tato-public-skills/plugins/antigravity-customizations/skills/creating-subagents/SKILL.md) | Guidance on spawning, managing, and communicating with asynchronous subagents. |
| **`creating-rules`** | [SKILL.md](file:///Users/tatopane/tato-public-skills/plugins/antigravity-customizations/skills/creating-rules/SKILL.md) | Best practices for configuring custom workspace rules and repeatable slash command workflows. |
| **`creating-hooks`** | [SKILL.md](file:///Users/tatopane/tato-public-skills/plugins/antigravity-customizations/skills/creating-hooks/SKILL.md) | Detailed schema reference and design guidelines for writing lifecycle event hooks (`hooks.json`). |
| **`creating-sidecars`** | [SKILL.md](file:///Users/tatopane/tato-public-skills/plugins/antigravity-customizations/skills/creating-sidecars/SKILL.md) | Instructions on developing, enabling, and managing persistent background sidecars and recurring scheduler tasks. |

---

## 🚀 How to Install

To use the plugin in your own Antigravity setup:

### Global Installation
Clone this repository or copy the `antigravity-customizations` folder to your global plugins directory:
```bash
cp -r plugins/antigravity-customizations ~/.gemini/config/plugins/
```

### Workspace Installation
To use it only in a specific workspace, copy the `antigravity-customizations` folder to your workspace's `.agents/plugins/` directory:
```bash
mkdir -p .agents/plugins
cp -r /path/to/plugins/antigravity-customizations .agents/plugins/
```
