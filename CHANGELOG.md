# Changelog

## 2026-05-01

### New Skills: `/resume` & `/resume-full`
- **`/resume`** — Recover last session's full conversation context. Reads the most recent session JSONL with jq pre-filtering (strips thinking/tool_result noise), plus git status and memory.
- **`/resume-full`** — Full project trajectory recovery. Last session in detail + summarized timeline of all historical sessions + broader git/memory context.

### Log Compaction Protocol
When session logs exceed 10 files, automatically compact older logs into a single summary file. Keeps the `log/` directory bounded without losing history.

## 2026-04-29

### Language Support
3 modes: English, Chinese, bilingual. Language-aware file naming, content generation, and on-the-fly language switching.

### File Reorganization
Deep organize (full scan) vs incremental organize (new files only). Adapts to project type and naming conventions.

### README Rewrite
Added problem statement, design philosophy diagram, component deep-dives, and Chinese translation.

## 2026-04-24

### Initial Release
5-component system: session logs, project wiki, constitution, file manager, execution checklist. Multi-tool support (Claude Code + Cursor).
