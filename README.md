# Egghead Wiki

> Knowledge base for the Egghead AI-Driven Multi-Agent Framework project.

## Purpose

Central documentation hub for all Egghead MAF artifacts — architecture decisions, agent definitions, skill implementations, workflow specs, and design plans. Single source of truth for the framework.

## Structure

```
egghead-wiki/
├── index.md              # Content catalog — start here
├── log.md                # Changelog (append-only)
├── SCHEMA.md             # Wiki schema and conventions
├── concepts/             # Framework-level documentation
│   ├── egghead-framework.md
│   ├── workflow-lifecycle.md
│   ├── skill-architecture.md
│   ├── runtime-integration.md
│   └── ...               # 19 concept pages total
├── entities/             # Agent role definitions
│   ├── intake.md
│   ├── orchestrator.md
│   ├── architect.md
│   └── ...               # 10 agent entities
├── plans/                # Design plans and test results
│   ├── 2026-06-22-smoke-test-skills.md
│   └── 2026-06-22-smoke-test-results.md
└── _archive/             # Superseded docs (archived, not deleted)
```

## Stats

| Metric | Count |
|--------|-------|
| Concept pages | 19 |
| Entity pages | 10 |
| Plans | 2 |
| Total pages | 31 |

## Source Repos

| Repo | Purpose |
|------|---------|
| `shuffahaqgzz/egghead` | This repo — Egghead MAF project docs and wiki |
| `shuffahaqgzz/hermes-wiki` | Hermes runtime wiki (config, setup, troubleshooting) |

## Conventions

- **Format:** Markdown with YAML frontmatter
- **Links:** Obsidian-style `[[double-bracket]]` wiki links
- **Log:** Append-only changelog in `log.md` — format: `## [YYYY-MM-DD] action | subject`
- **Archive:** Superseded content goes to `_archive/`, never deleted
- **Schema:** See `SCHEMA.md` for full field definitions

## Quick Links

- [Index](index.md) — full content catalog
- [Log](log.md) — chronological change history
- [Schema](SCHEMA.md) — page structure definitions
- [Skill Architecture](concepts/skill-architecture.md) — skill taxonomy and loading protocol
