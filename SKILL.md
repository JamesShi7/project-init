---
name: project-init
description: "Use when initializing or upgrading a project's management system. Triggers on /project-init, '初始化项目', 'setup project', '项目初始化', or when a project lacks management files (CLAUDE.md, PROJECT.md, session-handoff.md, TODO.md). Creates a 4-component system: session logs, project wiki, constitution tracking, and task execution. Supports fresh install and non-destructive upgrade for existing projects."
---

# Project Init — 4-Component Project Management System

Initialize a standardized project management system with 4 components:

```
上层（稳定原则）
┌─────────────────────────────────────┐
│  CLAUDE.md（项目宪法）← 人工确认     │
│  ↑ candidates（AI 自动收集候选）     │
└─────────────────────────────────────┘
        ↑ 抽象沉淀
中层（当前快照）
┌─────────────────────────────────────┐
│  PROJECT.md（项目 Wiki）← AI 自动同步 │
│  概览 / 结构 / 模块状态 / 文件索引    │
└─────────────────────────────────────┘
        ↑ 状态汇总
下层（事实流水）
┌────────────────────┐  ┌────────────────────┐
│  log/（会话日志）   │  │  TODO.md（执行清单）│
│  每次 end session   │  │  owner/deadline/    │
│  自动生成           │  │  deps               │
└────────────────────┘  └────────────────────┘
```

Core idea: bottom feeds top, top constrains bottom. Logs and TODOs are raw facts. Wiki is the current snapshot. Constitution is stable principles.

---

## Execution Flow

### Step 1: Detect Mode

Scan the project root for these files:

| File | Location |
|------|----------|
| CLAUDE.md | project root |
| PROJECT.md | project root |
| session-handoff.md | project root |
| TODO.md | project root |
| log/ | project root directory |
| .claude/candidates.md | project root |

Determine mode:
- **Fresh**: None of the above exist → create all 6
- **Upgrade**: Some exist → create only missing ones, never overwrite existing content

For upgrade mode with existing CLAUDE.md: check if it contains a `## 项目管理系统` section. If not, offer to append the system rules section. If it does, skip.

### Step 2: Interactive Questions

Ask the user these questions using AskUserQuestion:

1. **项目名称** — e.g., "达人营销AI", "Data Pipeline"
2. **一句话定义** — e.g., "AI-powered influencer marketing platform for US/EU markets"
3. **当前阶段** — e.g., "v1 MVP", "规划中", "已上线"
4. **GitHub 仓库** — optional, e.g., "org/repo-name"
5. **是否创建 Cursor 规则文件** — yes/no (default: yes)

### Step 3: Create Files

Create each file using the templates below, replacing `{{VARIABLES}}` with user answers.

For `{{DATE}}` use today's date in YYYY-MM-DD format.

When creating `log/` directory, also create `log/.gitkeep` (empty file) so git tracks the directory even when empty.

### Step 4: Output Report

After all files are created, output:

```
项目管理系统已初始化 ✓

文件状态：
  ✅ CLAUDE.md           — 项目宪法（已创建）
  ✅ PROJECT.md          — 项目 Wiki（已创建）
  ✅ session-handoff.md  — 接手指引（已创建）
  ✅ TODO.md             — 执行清单（已创建）
  ✅ log/                — 会话日志目录（已创建）
  ✅ .claude/candidates.md — 宪法候选池（已创建）
  ✅ .cursor/rules/      — Cursor 规则（已创建 / 已跳过）

下一步：
1. 根据项目需要，在 TODO.md 添加第一批任务
2. 正常开始工作
3. 结束时说 "end session" 即可自动记录

触发词速查：
  end session / 结束会话 / 收工 → 写 log + 全量同步
  review claude / 更新宪法     → 展示候选池逐条确认
  sync wiki / 同步项目         → 强制更新 PROJECT.md
  status / 项目现状            → 朗读 Wiki + handoff 摘要
```

---

## File Templates

### Template 1: CLAUDE.md

This is the most critical file — it's auto-loaded by Claude Code and defines all ongoing behavior. It contains both project info AND the system rules.

```
# {{PROJECT_NAME}} 项目指令

> 本文件由 Claude Code 自动加载，定义项目协作规则。

## 项目概况
- **产品：** {{ONE_LINE_DESCRIPTION}}
- **当前阶段：** {{CURRENT_STAGE}}
{{GITHUB_LINE}}

## 项目管理系统

本项目使用 4 组件管理系统。

### 触发词

| 你说 | AI 执行 |
|------|---------|
| end session / 结束会话 / 收工 | 写 log + 更新 handoff + 同步 Wiki + 校对 TODO + 收集宪法候选 + 输出中文总结 |
| review claude / 更新宪法 | 展示 .claude/candidates.md 逐条确认 |
| sync wiki / 同步项目 | 强制重扫并更新 PROJECT.md |
| status / 项目现状 | 朗读 PROJECT.md + session-handoff.md 摘要 |

### 文件职责

| 文件 | 谁写 | 何时 |
|------|------|------|
| CLAUDE.md | 人工确认 | review claude 时 |
| PROJECT.md | AI 自动 | end session + 文件结构变化时 |
| session-handoff.md | AI 自动 | end session 时 |
| TODO.md | AI + 人 | 随时 |
| log/session-*.md | AI | end session 时 |
| .claude/candidates.md | AI 自动 | 过程中识别到稳定规则时 |

### Session Start Protocol

每次会话开始时，先读 `PROJECT.md` 获取项目全貌。

### Session End Protocol

当用户说 "end session" / "结束会话" / "收工" 时，按顺序执行：

1. **写会话日志** → `log/session-YYYY-MM-DD-{主题slug}.md`
   - 同日多次会话用 slug 区分（如 `session-2026-04-21-prd-draft.md`）
2. **更新 session-handoff.md** → 刷新"当前进度 / 下一步"
3. **更新 PROJECT.md** → 如有结构/模块状态变化，同步更新
4. **更新 TODO.md** → 标记本次已完成的任务
5. **收集宪法候选** → 识别本次会话中的规则/偏好/边界，追加到 `.claude/candidates.md`
6. **输出中文总结** → 一段话概括本次做了什么，给你确认

### Session Log 格式

写入 `log/` 的每条日志遵循以下格式：

```markdown
# Session YYYY-MM-DD — {主题}

## 本次目标
## 关键操作（按时间顺序）
## 决策与理由
## 产出文件
## 未完事项 / 下次接手点
## 候选 CLAUDE.md 条目（如有）
```

### 宪法候选识别规则

AI 在工作过程中，遇到以下情况时自动追加条目到 `.claude/candidates.md`：
- 用户明确说"以后都这么做" / "这是规则" / "不要再…"
- 同一类决策在多次会话中连续出现
- 涉及命名规范、文件分层、协作流程的决定
- 涉及技术栈选择、架构约束的决定

**绝对不要直接修改 CLAUDE.md。** 所有候选条目必须经用户 review 后才写入。

### TODO 格式

TODO.md 中每条任务必须包含三要素：
```
- [ ] {任务描述}
  负责人：{name}｜截止：{date}｜依赖：{prerequisite}
```
用户给的任务缺要素时，主动追问补全。完成的任务勾选保留（不删除）。

## 项目特定规则

（此处由你手动添加项目特定的规则和偏好）
```

**Variable replacements:**
- `{{PROJECT_NAME}}` → answer to Q1
- `{{ONE_LINE_DESCRIPTION}}` → answer to Q2
- `{{CURRENT_STAGE}}` → answer to Q3
- `{{GITHUB_LINE}}` → `- **GitHub：** {{answer}}` if Q4 provided, else empty string
- `{{DATE}}` → today YYYY-MM-DD

---

### Template 2: PROJECT.md

The project wiki — single file that tells anyone/AI what this project is about.

```
# {{PROJECT_NAME}} — Project Wiki

> 最后同步：{{DATE}}（自动）

## 一句话定义
{{ONE_LINE_DESCRIPTION}}

## 当前阶段
{{CURRENT_STAGE}}

## 模块/章节地图
（AI 在后续 session 中自动填充）

## 文件结构
```
.
├── CLAUDE.md                   ← 项目宪法（人工确认）
├── PROJECT.md                  ← 本文件（AI 自动同步）
├── session-handoff.md          ← 接手指引（AI 自动）
├── TODO.md                     ← 执行清单
├── log/                        ← 会话日志
└── .claude/
    └── candidates.md           ← 宪法候选池
```

## 关键文件索引
| 文件 | 说明 |
|------|------|
| CLAUDE.md | 项目宪法，定义规则和边界 |
| PROJECT.md | 本文件，项目百科全貌 |
| session-handoff.md | 跨会话接手指引 |
| TODO.md | 执行任务清单 |
| .claude/candidates.md | 待确认的宪法候选条目 |

## 当前进度快照
| 模块 | 状态 | 备注 |
|------|------|------|
| （待填充） | | |

## 相关链接
{{GITHUB_LINK_LINE}}
```

**Variable replacements:**
- `{{GITHUB_LINK_LINE}}` → `- GitHub: https://github.com/{{answer}}` if provided, else `- （待添加）`

---

### Template 3: session-handoff.md

Cross-session handoff — tells the next session where things left off.

```
# Session Handoff — {{PROJECT_NAME}}

> 最后更新：{{DATE}} v0.1

## 项目目标
{{ONE_LINE_DESCRIPTION}}

## 核心产出文件
| 文件 | 状态 | 版本 | 说明 |
|------|------|------|------|
| （待填充） | | | |

## 当前进度
- 项目管理系统初始化完成

## 关键设计决策
| # | 决策 | 理由 | 日期 |
|---|------|------|------|
| 1 | 采用 4 组件管理系统 | Log + Wiki + Constitution + TODO | {{DATE}} |

## 迭代历史
| 版本 | 日期 | 变更 |
|------|------|------|
| v0.1 | {{DATE}} | 项目管理系统初始化 |

## 下一步
- [ ] 开始项目工作
```

---

### Template 4: TODO.md

Execution task list. Simple, actionable, with required metadata per task.

```
# {{PROJECT_NAME}} — TODO

> 每条任务必须包含：负责人 / 截止时间 / 依赖项
> 完成的任务勾选保留（不删除），作为执行历史

## 初始化
- [ ] 完善 PROJECT.md 中的模块地图
  负责人：｜截止：｜依赖：
- [ ] 添加第一批执行任务到 TODO.md
  负责人：｜截止：｜依赖：
```

---

### Template 5: .claude/candidates.md

Constitution candidate pool. AI writes here, user reviews via "review claude".

```
# CLAUDE.md 候选条目

> 以下条目由 AI 自动收集，等待人工 review。
> 触发 review：对 AI 说 "review claude" 或 "更新宪法"

## 待确认（待 review）
（暂无）

## 已驳回
（暂无）

## 已采纳（已写入 CLAUDE.md）
（暂无）
```

Ensure `.claude/` directory exists before writing this file.

---

### Template 6: .cursor/rules/project-system.mdc

Only create if user answered yes to Q5. Cursor rules file that mirrors the CLAUDE.md trigger behavior.

```
---
description: Apply at session start and when user says end session / review claude / sync wiki / status. Defines the 4-component project management system behavior.
---

# {{PROJECT_NAME}} — Project System Rules

## Session Start
Read PROJECT.md and session-handoff.md at the start of every conversation.

## End Session Protocol
When user says "end session" / "结束会话" / "收工":
1. Write log/session-YYYY-MM-DD-{slug}.md (summary, decisions, outputs, next steps)
2. Update session-handoff.md (refresh progress, next steps)
3. Update PROJECT.md if structure or module status changed
4. Update TODO.md (mark completed tasks)
5. Collect CLAUDE.md candidates → append to .claude/candidates.md
6. Output Chinese summary for user confirmation

## Trigger Words
| Trigger | Action |
|---------|--------|
| review claude / 更新宪法 | Show .claude/candidates.md for user to confirm each entry |
| sync wiki / 同步项目 | Force rescan and update PROJECT.md |
| status / 项目现状 | Read PROJECT.md + session-handoff.md summary aloud |

## File Roles
| File | Who writes | When |
|------|-----------|------|
| CLAUDE.md | Human only | review claude trigger |
| PROJECT.md | AI auto | end session + structure changes |
| session-handoff.md | AI auto | end session |
| TODO.md | AI + Human | anytime |
| log/session-*.md | AI | end session |
| .claude/candidates.md | AI auto | when stable rules identified |
```

---

## Upgrade Mode

When some files already exist:

### Rules
1. **Never overwrite** any existing file content
2. **Create only missing files** with templates
3. **For existing CLAUDE.md**: check if it contains `## 项目管理系统` section. If missing, offer to append the system rules block (the trigger words, file roles, session protocols). If present, skip.
4. **For existing session-handoff.md / TODO.md**: skip entirely
5. **For log/ directory**: create if missing (with `log/.gitkeep`), never touch existing log files
6. **For .claude/candidates.md**: create if missing, skip if exists
7. **For old .claude/memory/ directory**: if it exists, treat as legacy system. See Legacy Migration below.
8. **For existing .cursor/rules/**: check if `project-system.mdc` already exists. If yes, skip. If only other .mdc files exist, create `project-system.mdc` alongside them (not overwrite).

### Legacy Migration (.claude/memory/ → new system)

When `.claude/memory/` exists, it indicates an older project management setup. Handle as follows:

1. **Detect legacy artifacts:**
   - `.claude/memory/MEMORY.md` → old project wiki (content should be migrated to PROJECT.md)
   - `.claude/memory/session-*.md` → old session logs (should be moved to log/)
   - `~/.claude/projects/.../memory/MEMORY.md` → Claude auto-memory mirror (leave as-is)

2. **Migration actions (user-confirmed only):**
   - Move `.claude/memory/session-*.md` files → `log/archive/` (preserving history)
   - Merge `.claude/memory/MEMORY.md` content into new `PROJECT.md` (populating sections like 模块地图, 进度快照, etc.)
   - After migration, update the existing CLAUDE.md to remove or modify any sections that reference `.claude/memory/` paths (e.g., "记忆管理" rules) so they point to the new system paths instead.

3. **Never auto-migrate.** Present the user with a clear list of what would be moved/merged and ask for confirmation.

4. **Conflict resolution for CLAUDE.md append:**
   When appending the `## 项目管理系统` section to an existing CLAUDE.md that has old path references:
   - Note the specific sections that reference old paths (e.g., a "记忆管理" section pointing to `.claude/memory/`)
   - In the appended block, include a comment: `> 注意：旧版 .claude/memory/ 已迁移，所有新日志写入 log/`
   - Offer to update the conflicting old section to remove the now-stale references

### Upgrade Report

Output shows status of each file, plus any legacy migration suggestions:

```
项目管理系统升级检测 ✓

文件状态：
  ⚠️  CLAUDE.md           — 已存在，缺少「## 项目管理系统」，建议追加
  ✅ PROJECT.md           — 已创建（新增）
  ⏭️  session-handoff.md  — 已存在，跳过
  ⏭️  TODO.md             — 已存在，跳过
  ✅ log/                 — 已创建（新增）
  ✅ .claude/candidates.md — 已创建（新增）

Legacy 检测：
  ⚠️  .claude/memory/     — 发现旧版文件（5 个 session + MEMORY.md）
     建议迁移：session 文件 → log/archive/
     建议合并：MEMORY.md 内容 → PROJECT.md
     ⚠️  CLAUDE.md 现有「记忆管理」章节与新系统路径冲突
     建议更新旧章节指向新路径
     → 以上操作需你确认后执行，不会自动修改
```

---

## Common Mistakes

| Mistake | Correct Behavior |
|---------|-----------------|
| Overwriting existing files in upgrade mode | Never overwrite. Create only missing files. |
| Putting session logs in .claude/memory/ | Use log/ in project root. It's git-visible. |
| Auto-migrating legacy .claude/memory/ files | Always ask user to confirm migration. Never auto-move. |
| Leaving conflicting old+new path references in CLAUDE.md | When appending system section, flag and offer to resolve conflicts with existing sections. |
| AI directly editing CLAUDE.md | Only write to .claude/candidates.md. User confirms via "review claude". |
| Creating logs without updating Wiki/handoff | end session must do ALL steps, not pick and choose. |
| Empty TODO items without owner/deadline/deps | Always ask user to fill in the three required fields. |
| Forgetting to read PROJECT.md at session start | CLAUDE.md template explicitly instructs this. |
| Creating .cursor rules when user doesn't use Cursor | Only create when user answers yes to Q5. |
| Creating duplicate .cursor/rules/ files | Check if project-system.mdc already exists before creating. Create alongside existing .mdc files, never overwrite. |
