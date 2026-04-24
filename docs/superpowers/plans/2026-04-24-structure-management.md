# File Structure Management — Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Add a 5th component (STRUCTURE.md) to the project-init skill that provides intelligent file organization at each end session.

**Architecture:** Extend the existing SKILL.md with a new template, updated end session protocol, and file reorganization logic. The skill remains a single SKILL.md file — all changes are edits to that file.

**Tech Stack:** Markdown skill file, no code dependencies.

**Target file:** `/Users/sjw/.claude/skills/project-init/SKILL.md`

---

## File Structure

| File | Action | Purpose |
|------|--------|---------|
| SKILL.md | Modify | All changes go here |
| SKILL.md:1-4 (frontmatter) | Modify | Update description to mention 5-component system |
| SKILL.md:6-32 (diagram) | Modify | Update architecture diagram to show 5 components |
| SKILL.md:39-53 (Step 1) | Modify | Add STRUCTURE.md and .file-snapshot.json to detection |
| SKILL.md:57-65 (Step 2) | Modify | No changes needed |
| SKILL.md:67-100 (Step 3+4) | Modify | Add STRUCTURE.md creation to output report |
| SKILL.md:106-197 (Template 1: CLAUDE.md) | Modify | Add trigger word, file role, end session step 7 |
| SKILL.md:208-255 (Template 2: PROJECT.md) | Modify | Replace file structure section with reference |
| SKILL.md:339-378 (Template 6: cursor rules) | Modify | Add step 7 to cursor rules |
| SKILL.md (after Template 6) | Create | Add Template 7: STRUCTURE.md |
| SKILL.md (after Template 7) | Create | Add "File Reorganization Protocol" section |
| SKILL.md:444-458 (Common Mistakes) | Modify | Add structure management entries |

---

### Task 1: Update frontmatter and architecture diagram

**Files:**
- Modify: `SKILL.md:1-4` (frontmatter)
- Modify: `SKILL.md:6-32` (architecture diagram)

- [ ] **Step 1: Update frontmatter description**

Change the description from "4-component system" to "5-component system" and add STRUCTURE.md mention:

```markdown
---
name: project-init
description: "Use when initializing or upgrading a project's management system. Triggers on /project-init, '初始化项目', 'setup project', '项目初始化', or when a project lacks management files (CLAUDE.md, PROJECT.md, STRUCTURE.md, session-handoff.md, TODO.md). Creates a 5-component system: session logs, project wiki, file structure management, constitution tracking, and task execution. Supports fresh install and non-destructive upgrade for existing projects."
---
```

- [ ] **Step 2: Update architecture diagram**

Replace the diagram (lines 8-29) with:

```markdown
# Project Init — 5-Component Project Management System

Initialize a standardized project management system with 5 components:

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
│  STRUCTURE.md（文件管理规则）← AI 自动│
│  目录规则 / 匹配条件 / 整理历史       │
└─────────────────────────────────────┘
        ↑ 状态汇总
下层（事实流水）
┌────────────────────┐  ┌────────────────────┐
│  log/（会话日志）   │  │  TODO.md（执行清单）│
│  每次 end session   │  │  owner/deadline/    │
│  自动生成           │  │  deps               │
└────────────────────┘  └────────────────────┘
```

Core idea: bottom feeds top, top constrains bottom. Logs and TODOs are raw facts. Wiki is the current snapshot. Structure manages file organization. Constitution is stable principles.
```

- [ ] **Step 3: Commit**

```bash
git add SKILL.md
git commit -m "feat: update frontmatter and diagram for 5-component system"
```

---

### Task 2: Update Step 1 (Detect Mode)

**Files:**
- Modify: `SKILL.md:39-53` (Step 1: Detect Mode)

- [ ] **Step 1: Add new files to detection table**

In the "Scan the project root for these files" table, add two rows after `log/`:

```markdown
| STRUCTURE.md | project root |
| .claude/.file-snapshot.json | project root hidden file |
```

- [ ] **Step 2: Update mode descriptions**

Change "Fresh" to mention 7 files instead of 6, and update "Upgrade" wording:

```markdown
- **Fresh**: None of the above exist → create all 7 (including STRUCTURE.md)
- **Upgrade**: Some exist → create only missing ones, never overwrite existing content
```

- [ ] **Step 3: Commit**

```bash
git add SKILL.md
git commit -m "feat: add STRUCTURE.md to Detect Mode scan"
```

---

### Task 3: Update output report (Step 4)

**Files:**
- Modify: `SKILL.md:78-99` (Step 4: Output Report)

- [ ] **Step 1: Add STRUCTURE.md to file status list**

After the `.claude/candidates.md` line and before the Cursor rules line, add:

```markdown
  ✅ STRUCTURE.md        — 文件管理规则（已创建）
```

- [ ] **Step 2: Add trigger word to the speed reference**

In the 触发词速查 section, add a new line:

```markdown
  整理文件 / organize files → 扫描并整理文件结构
```

- [ ] **Step 3: Commit**

```bash
git add SKILL.md
git commit -m "feat: add STRUCTURE.md to output report"
```

---

### Task 4: Update CLAUDE.md template (Template 1)

**Files:**
- Modify: `SKILL.md:106-197` (Template 1: CLAUDE.md)

- [ ] **Step 1: Add trigger word to table**

In the 触发词 table, add a row after the `status` row:

```markdown
| 整理文件 / organize files | 扫描项目文件，按 STRUCTURE.md 规则整理 |
```

- [ ] **Step 2: Add STRUCTURE.md to file roles table**

In the 文件职责 table, add a row:

```markdown
| STRUCTURE.md | AI 自动 | end session + 文件结构变化时 |
| .claude/.file-snapshot.json | AI 自动 | end session 时 |
```

- [ ] **Step 3: Update end session protocol**

After step 5 (收集宪法候选), add step 6, and renumber step 6 (输出中文总结) to step 7:

```markdown
6. **整理文件结构** → 扫描项目文件，按 STRUCTURE.md 规则自动整理
   - 若 STRUCTURE.md 不存在：分析项目类型，生成规则表，整理现有文件
   - 若 STRUCTURE.md 已存在：匹配新增文件，扩展规则，移动文件
   - 更新 `.claude/.file-snapshot.json`
7. **输出中文总结** → 一段话概括本次做了什么，给你确认
```

- [ ] **Step 4: Commit**

```bash
git add SKILL.md
git commit -m "feat: add file reorganization to CLAUDE.md template"
```

---

### Task 5: Update PROJECT.md template (Template 2)

**Files:**
- Modify: `SKILL.md:208-255` (Template 2: PROJECT.md)

- [ ] **Step 1: Replace file structure section**

Replace the `## 文件结构` code block with a reference to STRUCTURE.md:

```markdown
## 文件结构
> 详细目录规则见 STRUCTURE.md

```
.
├── CLAUDE.md                   ← 项目宪法（人工确认）
├── PROJECT.md                  ← 本文件（AI 自动同步）
├── STRUCTURE.md                ← 文件管理规则（AI 自动维护）
├── session-handoff.md          ← 接手指引（AI 自动）
├── TODO.md                     ← 执行清单
├── log/                        ← 会话日志
└── .claude/
    ├── candidates.md           ← 宪法候选池
    └── .file-snapshot.json     ← 文件整理快照
```
```

- [ ] **Step 2: Add STRUCTURE.md to key file index**

Add a row to the 关键文件索引 table:

```markdown
| STRUCTURE.md | 文件管理规则，定义目录组织和匹配条件 |
```

- [ ] **Step 3: Commit**

```bash
git add SKILL.md
git commit -m "feat: update PROJECT.md template to reference STRUCTURE.md"
```

---

### Task 6: Add STRUCTURE.md template (Template 7)

**Files:**
- Create: After Template 6 section (after line ~378), insert new section

- [ ] **Step 1: Add Template 7 after Template 6**

Insert the following after the Template 6 section:

````markdown

---

### Template 7: STRUCTURE.md

File management rules. AI creates this on first end session or project init. Defines how files should be organized based on project type.

```
# {{PROJECT_NAME}} — 文件管理结构

> 最后更新：{{DATE}}

## 项目类型
{{AI 判断：代码项目 / 视频制作 / 商业文档 / 混合型 / 其他}}

## 排除规则
以下目录/文件不参与整理：
- .git/
- node_modules/
- __pycache__/
- .venv/
- dist/
- build/
- vendor/
- .claude/
- log/
- {{项目自定义排除}}

## 目录规则

| 路径 | 用途 | 匹配条件 | 优先级 |
|------|------|----------|--------|
| （AI 根据项目类型自动生成） | | | |

## 待分类
以下文件尚未归类（下次整理时处理）：
- （暂无）

## 整理历史
| 日期 | 操作 | 文件数 |
|------|------|--------|
| {{DATE}} | 初始化结构 | 0 |
```

**Variable replacements:**
- `{{PROJECT_NAME}}` → answer to Q1
- `{{DATE}}` → today YYYY-MM-DD

**Rule generation logic:**

When creating STRUCTURE.md for the first time, the AI must:

1. **Scan all files** in the project (excluding the default exclusion list)
2. **Determine project type** by analyzing file type distribution:
   - Majority `.py`/`.ts`/`.go`/`.java` etc. → 代码项目
   - Majority `.mp4`/`.mov`/`.prproj` etc. → 视频制作
   - Majority `.xlsx`/`.pdf`/`.docx` etc. → 商业文档
   - Mixed without clear majority → 混合型
3. **Generate rules** matching the project type. Use these as reference patterns:

**代码项目参考规则：**
| 路径 | 用途 | 匹配条件 | 优先级 |
|------|------|----------|--------|
| src/ | 源代码 | 按语言和模块组织 | 10 |
| tests/ / test/ | 测试文件 | *test*, *spec* | 20 |
| docs/ | 项目文档 | *.md, *.docx, *.pdf | 30 |
| config/ / conf/ | 配置文件 | *.yaml, *.toml, *.json (非 package.json) | 15 |

**视频制作参考规则：**
| 路径 | 用途 | 匹配条件 | 优先级 |
|------|------|----------|--------|
| footage/ | 原始素材 | *.mp4, *.mov, *.mxf | 10 |
| scripts/ | 脚本文档 | *.docx, *.md 含脚本相关 | 20 |
| editing/ | 剪辑工程 | *.prproj, *.drp, *.fcpxml | 30 |
| exports/ | 成片输出 | *.mp4 命名含 export/output | 40 |
| assets/ | 设计素材 | *.png, *.jpg, *.ai, *.psd | 15 |

**商业文档参考规则：**
| 路径 | 用途 | 匹配条件 | 优先级 |
|------|------|----------|--------|
| contracts/ | 合同法务 | *.pdf 含合同/协议 | 10 |
| finance/ | 财务报表 | *.xlsx, *.csv 含报表/预算 | 20 |
| teams/ | 团队子目录 | 按部门名归类 | 30 |
| meetings/ | 会议记录 | *.docx, *.md 含会议/纪要 | 15 |

4. **These are starting points.** Adapt rules based on actual file content and project context. The AI should refine, merge, or add rules as needed — not blindly copy templates.
````

- [ ] **Step 2: Commit**

```bash
git add SKILL.md
git commit -m "feat: add STRUCTURE.md template with rule generation logic"
```

---

### Task 7: Add File Reorganization Protocol section

**Files:**
- Create: After Template 7, before Upgrade Mode section

- [ ] **Step 1: Add the protocol section**

Insert after Template 7 and before the `## Upgrade Mode` section:

````markdown

---

## File Reorganization Protocol

Executed as step 6 of the end session protocol (after collecting constitution candidates, before outputting summary).

### Trigger

This protocol runs automatically when:
- User says "end session" / "结束会话" / "收工"
- User says "整理文件" / "organize files"

### Prerequisites

- Read STRUCTURE.md if it exists
- Read `.claude/.file-snapshot.json` if it exists

### Flow

```
1. Scan: recursively list all project files
   - Exclude: directories listed in STRUCTURE.md 排除规则 (or defaults if STRUCTURE.md doesn't exist)
   - Default exclusions: .git/, node_modules/, __pycache__/, .venv/, dist/, build/, vendor/, .claude/, log/

2. Compare against .claude/.file-snapshot.json
   - Identify new files (not in snapshot)
   - Identify moved/renamed files (path changed but content similar)
   - Skip files already in snapshot with same path

3. Branch on STRUCTURE.md existence:

   3a. STRUCTURE.md does NOT exist (first time):
       a. Analyze file type distribution across new+existing files
       b. Determine project type (代码/视频/商业/混合/其他)
       c. Generate directory rule table (see Template 7 reference rules)
       d. Create STRUCTURE.md
       e. For each existing file:
          - Match against rules (highest priority first)
          - If match: move file to target directory
          - If no match: add to 待分类 section
       f. Update PROJECT.md file structure section

   3b. STRUCTURE.md EXISTS (incremental update):
       a. For each new file:
          - Match against existing rules (highest priority first)
          - If match: move file to target directory
          - If no match:
            - AI analyzes file content and context
            - If category is inferable: add new rule to table + move file
            - If not inferable: add file path to 待分类 section
       b. Check if any rules need updating:
          - Did the user create new directories that suggest new categories?
          - Are there patterns of files in 待分类 that share a common trait?
       c. If rules were added/changed: update STRUCTURE.md

4. Safety checks before each file move:
   a. Cross-reference check: grep for old path in other files
      - If references found (import, link, config): update them
   b. Name collision check: does target directory already have a file with same name?
      - If yes: do NOT overwrite → add to 待分类 with collision note
   c. System file check: never move CLAUDE.md, PROJECT.md, STRUCTURE.md, session-handoff.md, TODO.md

5. Update .claude/.file-snapshot.json:
   {
     "lastScan": "{{current timestamp ISO format}}",
     "files": {
       "{{relative path}}": "{{date}}",
       ...
     }
   }
   - Add all newly organized files
   - Update paths for moved files
   - Remove entries for deleted files

6. Report in end session summary:
   - Number of files organized
   - Number of files added to 待分类
   - Any new rules added
   - Any structural changes made
```

### Important Constraints

- **Never move management files**: CLAUDE.md, PROJECT.md, STRUCTURE.md, session-handoff.md, TODO.md stay at project root
- **Never move files in exclusion list**: .git/, node_modules/, etc.
- **Preserve git history**: use `git mv` when in a git repo, not bare `mv`
- **Update references**: after moving a file, search for and update any imports/links to its old path
- **No overwrites**: if target has a same-name file, flag instead of clobbering
- **Respect human edits**: if user manually modified STRUCTURE.md rules, honor them. Only add new rules, never remove user-written rules without confirmation
````

- [ ] **Step 2: Commit**

```bash
git add SKILL.md
git commit -m "feat: add File Reorganization Protocol section"
```

---

### Task 8: Update Cursor rules template (Template 6)

**Files:**
- Modify: `SKILL.md:339-378` (Template 6: .cursor/rules/project-system.mdc)

- [ ] **Step 1: Add step 6 to cursor rules end session protocol**

In the End Session Protocol section of the cursor template, after step 5 and before the final step, add:

```markdown
6. File structure reorganization — scan files, match against STRUCTURE.md rules, organize (create STRUCTURE.md if missing)
7. Output Chinese summary for user confirmation
```

Renumber the old step 6 to step 7.

- [ ] **Step 2: Add new trigger word**

In the cursor template Trigger Words table, add:

```markdown
| 整理文件 / organize files | Scan files and reorganize according to STRUCTURE.md rules |
```

- [ ] **Step 3: Add STRUCTURE.md to cursor file roles table**

```markdown
| STRUCTURE.md | AI auto | end session + file structure changes |
| .claude/.file-snapshot.json | AI auto | end session |
```

- [ ] **Step 4: Commit**

```bash
git add SKILL.md
git commit -m "feat: add file reorganization to Cursor rules template"
```

---

### Task 9: Update Upgrade Mode section

**Files:**
- Modify: `SKILL.md:381-441` (Upgrade Mode section)

- [ ] **Step 1: Add STRUCTURE.md to upgrade rules**

After rule 6 (For .claude/candidates.md), add:

```markdown
7. **For STRUCTURE.md**: create if missing. If exists, never overwrite — user may have custom rules.
8. **For .claude/.file-snapshot.json**: create if missing (empty `{"lastScan":"","files":{}}`). If exists, skip.
```

Renumber existing rules 7-8 to 9-10.

- [ ] **Step 2: Update upgrade report template**

Add to the file status section:

```markdown
  ✅ STRUCTURE.md               — 已创建（新增）/ 已存在，跳过
  ✅ .claude/.file-snapshot.json — 已创建（新增）/ 已存在，跳过
```

- [ ] **Step 3: Commit**

```bash
git add SKILL.md
git commit -m "feat: add STRUCTURE.md handling to Upgrade Mode"
```

---

### Task 10: Update Common Mistakes table

**Files:**
- Modify: `SKILL.md:444-458` (Common Mistakes)

- [ ] **Step 1: Add new mistake entries**

Add these rows to the Common Mistakes table:

```markdown
| Moving files without checking cross-references | Always grep for imports/links to old path and update them |
| Overwriting files on name collision | If same-name file exists at target, flag for manual resolution instead |
| Moving management files (CLAUDE.md, PROJECT.md, etc.) | These must stay at project root. Never reorganize them |
| Using bare mv instead of git mv | In git repos, use git mv to preserve history |
| Removing user-written STRUCTURE.md rules | Only add new rules. Never remove without confirmation |
| Creating STRUCTURE.md rules without scanning project | Rules must be based on actual file analysis, not generic templates |
| Running file reorganization on excluded directories | Always check exclusion list before any file operation |
```

- [ ] **Step 2: Commit**

```bash
git add SKILL.md
git commit -m "feat: add file management entries to Common Mistakes"
```

---

### Task 11: Update session-handoff.md template (Template 3)

**Files:**
- Modify: `SKILL.md:260-293` (Template 3: session-handoff.md)

- [ ] **Step 1: Add STRUCTURE.md to core output files table**

In the 核心产出文件 table, add a row for STRUCTURE.md:

```markdown
| STRUCTURE.md | 文件管理规则 | v0.1 | 定义目录规则和匹配条件 |
```

- [ ] **Step 2: Commit**

```bash
git add SKILL.md
git commit -m "feat: add STRUCTURE.md to session-handoff template"
```

---

### Task 12: Final verification

- [ ] **Step 1: Read the complete updated SKILL.md and verify consistency**

Read the full file and check:
- All references to "4-component" changed to "5-component"
- STRUCTURE.md appears in all templates that list project files
- End session protocol has 7 steps (original 6 + file reorganization)
- Trigger words table includes "整理文件 / organize files"
- Common Mistakes table includes all new entries
- Template numbers are sequential (Template 1-7)
- No duplicate or contradictory instructions

- [ ] **Step 2: Fix any issues found**

- [ ] **Step 3: Final commit if fixes were needed**

```bash
git add SKILL.md
git commit -m "fix: consistency fixes after 5-component system update"
```
