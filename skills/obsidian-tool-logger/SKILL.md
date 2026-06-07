---
name: obsidian-tool-logger
description: Use when the user asks to install, download, add, set up, or deploy any software component. Also use when organizing, renaming, or standardizing skill documentation in the vault
---

# Obsidian Tool Logger

## Overview

Two workflows:

**Recording:** When installing any tool, automatically gather information, execute installation, determine category, assign a number by popularity (GitHub stars), and record everything into the user's Obsidian vault following their template format. If the tool is already documented, update it instead of duplicating.

**Deployment:** When setting up a new device, scan the vault for commonly-used tools (`常用: true`), infer the install commands from each document, execute them, and deploy corresponding opencode skills.

## Triggering

This skill activates in two modes:

**Recording mode** — when the user acquires new software:
- "安装 X" / "下载 X" / "添加 X"
- "设置 X" / "配置 X"
- "install X" / "setup X" / "add X"
- Any request involving acquiring new software

**Deployment mode** — when the user sets up a new device:
- "部署到这台电脑" / "安装常用" / "同步技能"
- "deploy my skills" / "setup this machine"

## Vault Configuration

```
VAULT_BASE = /Users/x1rz47/Library/CloudStorage/SynologyDrive-x1Rz47/5.个人资料/1.知识库/个人知识库/AI
TEMPLATE   = {VAULT_BASE}/Skills/00-Skills功能介绍模板.md
```

## Category Directories

| Type | Install Source | Target Path |
|------|---------------|-------------|
| Skill | `npx skills add`, `npm install -g` | `{VAULT_BASE}/Skills/General/` |
| 插件 | vscode, obsidian, browser extensions | `{VAULT_BASE}/插件/` |
| MCP | MCP server | `{VAULT_BASE}/MCP/` |
| Config | config files, dotfiles | `{VAULT_BASE}/Config/` |
| Other | brew, pip, direct download | `{VAULT_BASE}/其他/` |

## Naming Conventions

All skill document filenames in the vault must follow:

**Format:** `{NN}-{English-Name}.md`
- `NN`: 2-digit number (`01`, `02`...`99`)
- `English-Name`: Pascal-kebab-case, English only
- Example: `12-Playwright.md`, `04-Data-Visualization.md`

**Rules:**
- Only letters, numbers, and hyphens allowed
- First letter of each word capitalized (Pascal case), joined by hyphens
- No Chinese characters, no spaces, no underscores, no special characters
- Template files (`00-*`) are excluded from the numbering convention

**Independent numbering per directory:**
| Directory | Numbering Rule |
|-----------|---------------|
| `Skills/General/` | Sorted by GitHub stars (desc) + alphabetical |
| `Skills/Superpowers/` | Fixed order (01-14, matches superpowers release order) |
| `Skills/Gstack/` | Per-subdirectory independent numbering (01-N) |
| `Skills/Obsidian/` | Fixed order (01-04) |

When moving or renaming files, always parse frontmatter `工具名` and convert to Pascal-kebab-case.

## Recording Workflow

### Step 1: Identify the Tool

Determine:
- **Name**: What is it called?
- **Type**: skill / 插件 / MCP / npm package / config / other
- **Source**: GitHub / npm / brew / pip / direct download
- **Category**: Map source to target directory using the table above

### Step 2: Gather Information

Collect:
- Description from official docs/README
- GitHub URL and star count (use web search)
- Installation command
- Core features
- Dependencies
- Any warnings or notes

If GitHub fetch fails (network error, no GitHub repo), set `Github星标: N/A`. Do not block the workflow.

### Step 3: Execute Installation

Run the installation command. Wait for it to complete. Verify success.

### Step 4: Check for Existing Document

Before creating a new file:
1. Read all files in the target category directory
2. Parse frontmatter of each file for `工具名` and `aliases`
3. If a match is found → this is an **UPDATE**:
   - Update `更新日期` to today
   - Add a new entry to `更新功能`: e.g. "更新于 2026-06-06: 更新了核心功能描述"
   - Merge new information into existing sections
   - Do NOT change the file's number
   - Skip Steps 5-6, go directly to Step 7
4. If no match → this is a **NEW** entry, proceed to Step 5

### Step 5: Global Sort and Renumber (NEW entries only)

1. Read the `Github星标` frontmatter field from ALL existing files in the target directory
2. Parse star counts:
   - `12K` → 12000
   - `1.5K` → 1500
   - `N/A` → 0 (sorts to end)
3. Sort all entries (new + existing) by:
   - Primary: star count descending
   - Secondary: tool name alphabetically (for equal star counts)
4. Assign new numbers: `01`, `02`, `03`...
5. Determine the filename from `工具名` in frontmatter:
   - Convert to Pascal-kebab-case: `Find Skills` → `Find-Skills`, `Create Agents.md` → `Create-Agentsmd`
   - Result: `{NN}-{Pascal-Kebab-Name}.md`
6. Rename files on disk to match: `{NN}-{Pascal-Kebab-Name}.md`

Edge case — tools with `N/A` stars: always sort to the end, ordered alphabetically among themselves.

### Step 6: Generate Obsidian Document

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

### Step 7: Save and Confirm

Write to `{VAULT_BASE}/{category}/{filename}`.
Confirm to the user that the tool is installed and documented.

## Deployment Workflow (New Device Setup)

This workflow runs independently from the recording workflow above. Use it when setting up a new machine.

### Step D1: Scan for Favorites

1. Read all .md files in `{VAULT_BASE}/Skills/General/`
2. Parse frontmatter of each file, filter for `常用: true`
3. If none found, report "没有标记常用的工具" and stop

### Step D2: Install Each Favorite

For each file with `常用: true`:

1. Read the document body, focusing on the **常用命令/语法** and **核心功能** sections
2. Infer the installation command(s) from the content
3. Determine what to install:
   - **System tool** (brew/pip/npm) → run the install command directly
   - **OpenCode skill** → create `~/.config/opencode/skills/{tool-name}/SKILL.md`
4. Check if already installed — skip if present
5. Execute the install command. Verify success.
6. Record success or failure

### Step D3: Report Results

Present a summary table:

| Tool | Status |
|------|--------|
| Find-Skills | ✅ 已完成 |
| Data-Visualization | ✅ 已完成 |
| Task-Management | ⏭️ 已存在 |

## Quality Checks

- Verify the file was written correctly (read back first few lines)
- GitHub star count should be accurate (fetched, not guessed)
- Installation commands must be the actual commands used
- Sections should follow template structure
- No placeholder text left in the document
- After renumbering, verify no file is missing or has a wrong number

## Red Flags

Stop and re-evaluate if you catch yourself thinking:

| Thought | Reality |
|---------|---------|
| "这个工具我很熟，不用查资料了" | 必须查官方文档/README |
| "star 数大概记得，不用查" | 必须现查，从不用记忆 |
| "先安装了再说" | 必须先查信息 → 再安装 → 最后记录 |
| "这个不需要模板" | 必须使用 `00-Skills功能介绍模板.md` |
| "记录到目录就行了，不用管编号" | 必须全局排序并重编号 |
| "它就是一个小工具，不用查 GitHub" | 每个工具都要查，无 GitHub 的标 N/A |
| "这跟已有的工具很像，直接跳过" | 必须检查确切匹配，不能猜 |

## Edge Cases

| Case | Handling |
|------|----------|
| Template file not found | Stop with error: "模板文件不存在：{TEMPLATE}" |
| GitHub fetch fails | Set `Github星标: N/A`, do not block |
| No GitHub repository | Set `Github连接: 无`, `Github星标: N/A` |
| Tool already documented | Update existing file, do not duplicate |
| Category directory doesn't exist | Create it automatically |
| Star count format varies | Parse: `12K` → 12000, `1.5K` → 1500, `N/A` → 0 |
| Multiple files share the same star count | Sort alphabetically by tool name |
| File rename fails during renumbering | Stop and report which file failed |
| No existing files in directory | Number new file as `01` |
| No `常用: true` tools found during deployment | Report "没有标记常用的工具" and stop |
| Install command is ambiguous | Ask user to clarify before proceeding |
| Tool is already installed | Skip, do not reinstall |
| Install command fails | Record the error, continue with next tool |

## Rationalization Table

| Excuse | Reality |
|--------|---------|
| "I know this tool well enough" | What you know ≠ what belongs in documentation. Fetch fresh info. |
| "The stars are roughly correct" | Roughly ≈ wrong. Fetch exact count. |
| "Renumbering is tedious, I'll skip it" | Without sorting, the numbering system breaks. Never skip. |
| "This is just a small utility" | Small tools need documentation too. Follow the full workflow. |
| "The template is overkill for this" | The template exists for a reason. Use it. Every time. |

## Anti-Patterns

- Don't skip info gathering ("I know this tool already")
- Don't write the doc before confirming installation succeeded
- Don't use star counts from memory — always fetch fresh
- Don't skip the template — user has a specific format they want
- Don't skip creating category directories if they don't exist
- Don't skip global renumbering — it's required on every new addition
- Don't guess about tool existence — read frontmatter to check
- Don't install tools the user didn't mark as `常用` — only deploy what's explicitly flagged
- Don't reinstall already-present tools without asking
