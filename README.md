# project-init

A Claude Code skill that sets up a standardized 4-component project management system for any project.

## What It Does

Runs `/project-init` once in any project directory to create:

```
project-root/
├── CLAUDE.md                   ← Constitution (human-confirmed rules)
├── PROJECT.md                  ← Wiki (auto-synced project overview)
├── session-handoff.md          ← Cross-session handoff (auto-updated)
├── TODO.md                     ← Execution checklist (owner/deadline/deps)
├── log/                        ← Session logs (auto-generated)
└── .claude/
    └── candidates.md           ← Constitution candidate pool (AI-collected)
```

**4 components, layered by stability:**

| Layer | File | Purpose |
|-------|------|---------|
| Top (stable) | CLAUDE.md | Project constitution — rules and boundaries |
| Middle (snapshot) | PROJECT.md | Project wiki — single-file overview |
| Bottom (facts) | log/ + TODO.md | Session logs and task tracking |

Bottom feeds top, top constrains bottom.

## Trigger Words (after setup)

| You say | AI does |
|---------|---------|
| `end session` / `结束会话` / `收工` | Write log + update handoff + sync Wiki + check TODO + collect constitution candidates |
| `review claude` / `更新宪法` | Show candidates for you to confirm |
| `sync wiki` / `同步项目` | Force-update PROJECT.md |
| `status` / `项目现状` | Read Wiki + handoff summary |

## Install

Clone into your Claude Code skills directory:

```bash
# Method 1: Clone directly
git clone https://github.com/JamesShi7/project-init.git ~/.claude/skills/project-init

# Method 2: If you already have skills, just add this one
cd ~/.claude/skills
git clone https://github.com/JamesShi7/project-init.git
```

## Use

In any project directory:

```
/project-init
```

Answer 5 questions (project name, description, stage, GitHub URL, Cursor rules?), and all files are created.

**Supports upgrade mode**: if a project already has some files (CLAUDE.md, TODO.md, etc.), it only creates missing ones and never overwrites existing content.

## Requirements

- [Claude Code](https://claude.com/claude-code) CLI
- Optionally: [Cursor](https://cursor.sh) (if you want Cursor rules generated)

## License

MIT
