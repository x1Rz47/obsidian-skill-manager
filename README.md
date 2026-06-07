# AI - OpenCode Tools & Skills

Personal OpenCode skills and configuration.

## Structure

```
AI/
├── opencode/            ← OpenCode 配置
│   ├── AGENTS.md           代理指令
│   ├── opencode.jsonc      OpenCode 设置
│   └── .opencode/          OpenCode 内部配置
├── skills/              ← 自定义 skills
│   └── obsidian-tool-logger/
│       └── SKILL.md
├── scripts/             ← 工具脚本
├── docs/                ← 文档/笔记
└── README.md
```

## Skills

### obsidian-tool-logger

安装任何工具时自动记录到 Obsidian 知识库。详见 [SKILL.md](skills/obsidian-tool-logger/SKILL.md)。

## Install

```bash
npx skills add x1Rz47/AI@obsidian-tool-logger
```

## Tips for other agents reading this

- `opencode/AGENTS.md` 包含 agent 的指令配置
- `opencode/opencode.jsonc` 是 OpenCode 的插件和设置
- `skills/` 下每个目录是一个可安装的 skill
