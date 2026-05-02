# Update Log

## v1.0.0 (2026-05-01)

### Session Recovery
- **`/resume`** — Recover last session's full conversation context via jq-filtered JSONL reading
- **`/resume-full`** — Full project trajectory recovery with historical session timeline
- Both skills included in the repo — no separate installation needed

### Log Compaction Protocol
When session logs exceed 10 files, automatically compact older logs into a single summary file. Keeps the `log/` directory bounded without losing history.

## v0.4.0 (2026-04-29)

### Multi-Language + File Management
- 3 language modes: English, Chinese, bilingual with language-aware file naming
- Deep organize vs incremental organize for file reorganization
- Language Change Protocol with on-the-fly switching
- Added "end session" / "收工" as triggers for full end-of-session protocol

## v0.3.0 (2026-04-27)

### Constitution + File Manager
- Added STRUCTURE.md as 5th component for intelligent file management
- Constitution candidate system — AI collects rules, human confirms
- Added "organize files" as direct trigger for file reorganization

## v0.2.0 (2026-04-25)

### README Rewrite + Chinese Support
- Rewrote README with problem statement, design philosophy, component deep-dives
- Added Chinese README (`README_zh.md`) with language switcher
- Added Karpathy Guidelines to CLAUDE.md template

## v0.1.0 (2026-04-24)

### Initial Release
- 5-component system: session logs, project wiki, constitution, file manager, execution checklist
- Multi-tool support (Claude Code + Cursor)
- One-command setup with upgrade mode
