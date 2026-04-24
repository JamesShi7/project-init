# File Structure Management — Design Spec

> Date: 2026-04-24
> Status: Approved
> Affects: project-init skill (SKILL.md)

## Background

The project-init skill manages a 4-component project system (CLAUDE.md, PROJECT.md, session-handoff.md, TODO.md). It lacks automated file organization — projects accumulate files without structure, and there's no mechanism to maintain a consistent directory layout over time.

## Goal

Add a file management system that:
1. Analyzes project files and creates organization rules
2. Automatically organizes files according to those rules at each "end session"
3. Evolves the rules as the project grows
4. Works for any project type (code, video, business docs, mixed)
5. Handles both new projects and existing projects with pre-existing files

## Design

### 1. New Component: STRUCTURE.md

Adds a 5th component to the system:

```
上层（稳定原则）
┌─────────────────────────────────────┐
│  CLAUDE.md（项目宪法）               │
└─────────────────────────────────────┘
        ↑
中层（当前快照）
┌─────────────────────────────────────┐
│  PROJECT.md（项目 Wiki）             │
│  STRUCTURE.md（文件管理规则）← NEW   │
└─────────────────────────────────────┘
        ↑
下层（事实流水）
┌────────────────────┐  ┌────────────────────┐
│  log/（会话日志）   │  │  TODO.md（执行清单）│
└────────────────────┘  └────────────────────┘
```

**STRUCTURE.md** defines how files should be organized. It is rule-driven, AI-generated, and human-editable.

**Relationship to PROJECT.md**: PROJECT.md records "what the project is". STRUCTURE.md records "where files should go". PROJECT.md's file structure section will reference STRUCTURE.md instead of maintaining its own directory tree.

### 2. STRUCTURE.md Format

```markdown
# {{PROJECT_NAME}} — 文件管理结构

> 最后更新：{{DATE}}

## 项目类型
{{AI-determined: 代码项目 / 视频制作 / 商业文档 / 混合型 / ...}}

## 排除规则
以下目录/文件不参与整理：
- .git/
- node_modules/
- __pycache__/
- .venv/
- dist/ / build/
- {{project-specific exclusions}}

## 目录规则

| 路径 | 用途 | 匹配条件 | 优先级 |
|------|------|----------|--------|
| src/ | 源代码 | 按语言和模块组织 | 10 |
| tests/ | 测试 | *test*.py, *_spec.* | 20 |
| docs/ | 文档 | *.md, *.docx, *.pdf | 30 |

## 待分类
以下文件尚未归类（下次整理时处理）：
- （暂无）

## 整理历史
| 日期 | 操作 | 文件数 |
|------|------|--------|
| {{DATE}} | 初始化结构 | 0 |
```

### 3. Rule Matching System

Matching conditions support three granularity levels:

1. **File extension**: `*.py`, `*.mp4`, `*.xlsx`
2. **Filename pattern**: `*test*`, `*spec*`, `*合同*`
3. **Content/context inference**: AI determines based on file content (fallback when extension/pattern don't match)

Priority: higher numbers match first, preventing conflicts (e.g., `tests/` matches before generic `*.py → src/`).

### 4. Initial Scenario Handling

**Empty project**: Create STRUCTURE.md skeleton with empty rule table, mark project type as "待定". Rules populate as files are added.

**Existing project with files**: AI scans all files, analyzes types and content, determines project type, generates rule table, then reorganizes existing files to match rules.

### 5. End Session Flow Update

Insert step 7 into the existing 6-step end session protocol:

```
1. Write session log → log/
2. Update session-handoff.md
3. Update PROJECT.md
4. Update TODO.md
5. Collect constitution candidates → .claude/candidates.md
6. Output Chinese summary
7. File structure reorganization ← NEW
```

### 6. Step 7 Detailed Flow

```
7.1 Scan project files
    - Recursively list all files (excluding directories in exclusion rules)
    - Compare against last file snapshot (.claude/.file-snapshot.json)

7.2 Check STRUCTURE.md status
    ├── Does not exist → 7.2a First-time build
    └── Exists         → 7.2b Incremental update

7.2a First-time build
    - Analyze file type distribution → determine project type
    - Generate directory rule table
    - Create STRUCTURE.md
    - Move existing files according to rules
    - Update PROJECT.md reference

7.2b Incremental update
    - Identify new/changed files
    - Match each against existing rules
    ├── Match found → move file to corresponding directory
    ├── No match →
    │   ├── AI can infer category → add rule + move file
    │   └── Cannot infer → add to "待分类" section
    - If rules changed → update STRUCTURE.md
    - Check if previously organized files need relocation (after rule updates)

7.3 Update file snapshot → .claude/.file-snapshot.json
```

### 7. File Snapshot

Tracks which files have been processed, avoiding redundant work:

```json
{
  "lastScan": "2026-04-24T18:00:00",
  "files": {
    "src/main.py": "2026-04-24",
    "docs/prd.md": "2026-04-24"
  }
}
```

Stored at `.claude/.file-snapshot.json`.

### 8. Safety Mechanisms

- **Built-in exclusions**: `.git/`, `node_modules/`, `dist/`, `__pycache__/`, `.venv/`, `.claude/`, `build/`, `vendor/` are never touched by default
- **Cross-reference check**: Before moving a file, grep for references to its old path (imports, links, config). If found, update references synchronously
- **Name collision**: If target location already has a file with the same name, do not overwrite — add to "待分类" and flag for manual resolution
- **Human-editable rules**: User can manually edit STRUCTURE.md to override AI rules at any time

## Changes to Existing Files

### SKILL.md Changes

1. **File Templates section**: Add Template 7 for STRUCTURE.md
2. **Step 1 (Detect Mode)**: Add STRUCTURE.md and `.claude/.file-snapshot.json` to detection scan
3. **Step 3 (Create Files)**: Add STRUCTURE.md creation logic
4. **End Session Protocol**: Add step 7 (file structure reorganization)
5. **CLAUDE.md template**: Add STRUCTURE.md to trigger words table and file roles table
6. **Common Mistakes**: Add entries for structure management edge cases

### CLAUDE.md Template Changes

- Add "整理文件 / organize" trigger word → run step 7 independently
- Add STRUCTURE.md to file roles table
- Update end session trigger to include step 7

### PROJECT.md Template Changes

- Replace `## 文件结构` section with a reference to STRUCTURE.md
- Add STRUCTURE.md to key file index

## Examples by Project Type

### Code Project
```
| 路径 | 用途 | 匹配条件 | 优先级 |
|------|------|----------|--------|
| src/ | 源代码 | 按语言和模块组织 | 10 |
| tests/ | 测试 | *test*.py, *_spec.* | 20 |
| docs/ | 文档 | *.md, *.docx | 30 |
| config/ | 配置 | *.yaml, *.toml, *.json (非 package.json) | 15 |
```

### Video Production Project
```
| 路径 | 用途 | 匹配条件 | 优先级 |
|------|------|----------|--------|
| footage/ | 原始素材 | *.mp4, *.mov, *.mxf | 10 |
| scripts/ | 脚本文档 | *.docx, *.md, *.txt 含脚本相关内容 | 20 |
| editing/ | 待剪辑项目 | *.prproj, *.drp, *.fcpxml | 30 |
| exports/ | 成片输出 | *.mp4 (路径/命名含 export/output) | 40 |
| assets/ | 素材资源 | *.png, *.jpg, *.ai, *.psd | 15 |
```

### Business Document Project
```
| 路径 | 用途 | 匹配条件 | 优先级 |
|------|------|----------|--------|
| contracts/ | 合同法务 | *.pdf 含合同/协议 | 10 |
| finance/ | 财务报表 | *.xlsx, *.csv 含报表/预算 | 20 |
| teams/ | 各团队子目录 | 按部门名自动归类 | 30 |
| meetings/ | 会议记录 | *.docx, *.md 含会议/纪要 | 15 |
```
