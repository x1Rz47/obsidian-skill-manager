---
name: obsidian-tool-logger
description: Use when the user says "install", "download", "add", or "设置" a tool, skill, plugin, MCP server, npm package, or any other software component. Triggers when the user wants to record tool information into their Obsidian vault.
---

# Obsidian Tool Logger

## Overview

When installing any tool (skill, plugin, MCP, npm package, config, etc.), automatically gather information, execute installation, and record everything into the user's Obsidian vault following their template format.

## Triggering

This skill activates when the user says:
- "安装 X" / "下载 X" / "添加 X"
- "设置 X" / "配置 X"
- "install X" / "setup X" / "add X"
- Any request involving acquiring new software

## Vault Configuration

```
VAULT_BASE = /Users/x1rz47/Library/CloudStorage/SynologyDrive-x1Rz47/5.个人资料/1.知识库/个人知识库/AI
TEMPLATE   = {VAULT_BASE}/Skills/00-Skills功能介绍模板.md
```

## Category Directories

| Type | Path | Filename Pattern |
|------|------|-----------------|
| Skill | `{VAULT_BASE}/Skills/` | `{tool-name}.md` |
| 插件 | `{VAULT_BASE}/插件/` | `{tool-name}.md` |
| MCP | `{VAULT_BASE}/MCP/` | `{tool-name}.md` |
| Config | `{VAULT_BASE}/Config/` | `{tool-name}.md` |
| Other | `{VAULT_BASE}/其他/` | `{tool-name}.md` |

## Workflow

### Step 1: Identify the Tool

Determine:
- **Name**: What is it called?
- **Type**: skill / 插件 / MCP / npm package / config / other
- **Source**: GitHub / npm / brew / pip / direct download

### Step 2: Gather Information

Collect:
- Description from official docs/README
- GitHub URL and star count (use web search)
- Installation command
- Core features
- Dependencies
- Any warnings or notes

### Step 3: Execute Installation

Run the installation command. Wait for it to complete. Verify success.

### Step 4: Generate Obsidian Document

Read the template file at `{TEMPLATE}`, then fill in each section:

**Frontmatter:**
```yaml
---
工具名: <tool-name>
aliases:
  - <alias-1>
  - <alias-2>
标签:
  - <type-tag>
  - <domain-tags>
Github连接: <github-url>
Github星标: <star-count>
创建日期: <today>
更新日期: <today>
常用: false
---
```

**Sections to fill:**
- **更新功能** — "首次安装" for new tools
- **Skills简介** — 1-2 sentences summarizing what it does
- **主要解决的问题** — ❌/✅ format, at least 3 pain points
- **主要使用场景** — Bullet list with **bold** scene names
- **核心功能** — ### headers with feature descriptions
- **常用命令/语法** — Code blocks with actual commands
- **注意事项** — Important caveats
- **参考链接** — Official docs, GitHub, related resources

### Step 5: Save and Confirm

Write to `{VAULT_BASE}/{category}/{tool-name}.md`.
Confirm to the user that the tool is installed and documented.

## Quality Checks

- Verify the file was written correctly (read back first few lines)
- GitHub star count should be accurate (fetched, not guessed)
- Installation commands must be the actual commands used
- Sections should follow template structure
- No placeholder text left in the document

## Anti-Patterns

- Don't skip info gathering ("I know this tool already")
- Don't write the doc before confirming installation succeeded
- Don't use star counts from memory — always fetch fresh
- Don't skip the template — user has a specific format they want
- Don't skip creating category directories if they don't exist
